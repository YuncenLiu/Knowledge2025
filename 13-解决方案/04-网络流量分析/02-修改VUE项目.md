---
createTime: 2025-05-13
---
原本的 Vue 项目，大多使用 xxx.com/#/xxxx 这种结构显示页面
![](images/Pasted%20image%2020250513133519.png)

所以无法完美获取用户访问地址，因此，需要将 vue 项目修改成没有 /#/...  路径的地址

xiang-admin 项目（Vue2.x）

```js
export const createRouter = () => new VueRouter({
  mode: 'history', // 使用 HTML5 History 模式
  base: '/', // 配置应用的基础路径，若应用是部署在子目录，可以设置为子目录路径
  ....
```


sakura 项目 (Vue3.x)

```js
import { createRouter, createWebHistory } from 'vue-router'

const router = createRouter({
  // history: createWebHashHistory(),
  // 改为 createWebHistory
  history: createWebHistory(),
```

此时前端改完了，部署上线之后，会发现，大部分页面都可以正常访问
但是一刷新浏览器，就会404错误

因此调整 Nginx 配置文件

```conf
location / {
    root /home/xiang/service/smart-admin/;
    # 仅添加这一行
    try_files $uri $uri/ /index.html last;
    index index.html;
    expires -1;
}
```

重启 Nginx 配置文件之后，即可解决所有问题，并且 umami 可以成功记录访问地址