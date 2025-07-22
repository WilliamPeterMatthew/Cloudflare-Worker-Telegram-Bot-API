# Cloudflare-Worker-Telegram-Bot-API
Cloudflare Worker 用于反向代理 Telegram Bot API

```js
// Cloudflare Worker 用于反向代理 Telegram Bot API
// 可用于 Telegram Bot 在被限制地区稳定通信

const TELEGRAM_API = "https://api.telegram.org";

export default {
  async fetch(request, env, ctx) {
    const url = new URL(request.url);

    // 将原始请求路径拼接到 Telegram API 基础地址
    url.hostname = TELEGRAM_API.replace(/^https?:\/\//, "");

    const newRequest = new Request(url.toString(), {
      method: request.method,
      headers: request.headers,
      body: request.body,
      redirect: "manual"
    });

    // 添加 CORS 头部，允许跨域访问（可选）
    const response = await fetch(newRequest);
    const modifiedHeaders = new Headers(response.headers);
    modifiedHeaders.set("Access-Control-Allow-Origin", "*");
    modifiedHeaders.set("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE, OPTIONS");
    modifiedHeaders.set("Access-Control-Allow-Headers", "Content-Type, Authorization, *");

    return new Response(response.body, {
      status: response.status,
      statusText: response.statusText,
      headers: modifiedHeaders
    });
  }
};
```
