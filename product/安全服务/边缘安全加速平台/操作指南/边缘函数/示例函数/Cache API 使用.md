在边缘函数中，使用 `fetch API` 获取远程资源 jquery，借助 `Cache API` 把资源缓存到 EdgeOne 边缘节点，缓存时长为 10s。

## 示例代码 

```typescript
async function fetchJquery(event, request) {
  const cache = caches.default;
  // 缓存没有命中，回源并缓存
  let response = await fetch(request);
  
  // 在响应头添加 Cahe-Control，设置缓存时长 10s
  response.headers.append('Cache-Control', 's-maxage=10');
  event.waitUntil(cache.put(request, response.clone()));
  
  // 未命中缓存，设置响应头标识
  response.headers.append('x-edgefunctions-cache', 'miss');
  return response;
}

async function handleEvent(event) {
  // 资源地址，也作为缓存键
  const request = new Request('https://static.cloudcachetci.com/qcloud/main/scripts/release/common/vendors/jquery-3.2.1.min.js');
  // 缓存默认实例
  const cache = caches.default;

  try {
    // 获取关联的缓存内容，缓存过，接口底层不主动回源，抛出 504 错误
    let response = await cache.match(request);

    // 缓存不存在，重新获取远程资源
    if (!response) {
      return fetchJquery(event, request);
    }

    // 命中缓存，设置响应头标识
    response.headers.append('x-edgefunctions-cache', 'hit');

    return response;
  } catch (e) {
    await cache.delete(request);
    // 缓存过期或其他异常，重新获取远程资源
    return fetchJquery(event, request);
  }
}

addEventListener('fetch', (event) => {
  event.respondWith(handleEvent(event));
});
```

## 示例预览

在浏览器地址栏中输入边缘函数触发规则，即可预览到示例效果。

- 未命中缓存。

<img src="https://user-images.githubusercontent.com/117053395/208015306-5d9ba9b4-b1a7-48c0-b2c1-868ade979cf7.png" width=609px>

- 命中缓存。

<img src="https://user-images.githubusercontent.com/117053395/208015231-8c93d07e-a919-49c3-9ad5-257b32f8f27b.png" width=609px>


## 相关参考
- [Runtime APIs: Cache](https://cloud.tencent.com/document/product/1552/81893)
- [Runtime APIs: Fetch](https://cloud.tencent.com/document/product/1552/81897)
- [Runtime APIs: FetchEvent](https://cloud.tencent.com/document/product/1552/81899)
- [Runtime APIs: Response](https://cloud.tencent.com/document/product/1552/81917)
