在边缘函数中，使用 Cookies 做访问计数，当在浏览器访问边缘函数服务时，请求计数加 1。

## 示例代码

```typescript
async function handleRequest(request) {
  // 获取当前请求的 Cookies 
  const cookies = request.getCookies();
  const cookieCount = cookies.get('count');
  // 计数累加
  const count = Number(cookieCount && cookieCount.value || 0) + 1;
  // 更新 cookie 的计数
  cookies.set('count', String(count));

  const response = new Response(`The count is: ${count}`);
  // 设置响应 cookies
  response.setCookies(cookies);
  return response;
}
  
addEventListener('fetch', (event) => {
  event.respondWith(handleRequest(event.request));
});
```

## 示例预览

在浏览器地址栏中输入边缘函数触发规则，即可预览到示例效果。

<img src="https://user-images.githubusercontent.com/117053395/207915886-87f33402-7ce9-4ce0-b6d0-19ae17b9b045.png" width=609px>

## 相关参考
- [Runtime APIs: Cookies](https://cloud.tencent.com/document/product/1552/83932)
- [Runtime APIs: Response](https://cloud.tencent.com/document/product/1552/81917)
- [Runtime APIs: Request](https://cloud.tencent.com/document/product/1552/81902)
