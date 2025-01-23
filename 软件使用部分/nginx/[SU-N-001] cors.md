# [SU-N-001] Nginx CORS 配置

## 什么是 CORS

CORS 即跨域资源分析（Cross-Origin Resource Sharing），跨域并不仅仅是主域名不同，而是协议、域名、端口不同都是跨域。举例如下：

|                   当前 URL                    |                  被请求页面 URL                  | 是否跨域 |            跨域原因            |
| :------------------------------------------: | :--------------------------------------------: | :------: | :---------------------------: |
|           `http://www.example.com`           |      `http://www.example.com/index.html`       |    否    | 同源（协议、域名、端口号均一致） |
|   <mark>`http://`</mark>`www.example.com`    |    <mark>`https://`</mark>`www.example.com`    |    是    |    协议不一致（http/https）    |
|  `http://www.`<mark>`example`</mark>`.com`   |     `http://www.`<mark>`test`</mark>`.com`     |    是    |   主域名不同（example/test）   |
|  ` http://`<mark>`www`</mark>`.example.com`  |   `http://`<mark>`blog`</mark>`.example.com`   |    是    |     子域名不同（www/blog）     |
| `https://www.example.com:`<mark>`443`</mark> | `htttps://www.example.com:`<mark>`1443`</mark> |    是    |     端口号不同（443/1443）     |