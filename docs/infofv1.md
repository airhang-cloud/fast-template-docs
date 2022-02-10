# 代码目录

```javascript
├─api                             //api相关存放
│  ├─Demo
│  └─User
├─assets                          //静态资源存放
├─components                      //自定义组件存放
│  ├─ContainerBox
│  └─G2Template
├─router                          //路由配置存放
│  └─BussinessRouter
├─store                           //vuex相关配置存放
│  └─Modules
├─utils                           //工具封装存放
└─views                           //界面视图存放
    ├─Layout                        //前端界面布局相关
    │  ├─HeaderTool
    │  │  └─View
    │  └─Nav
    └─Pages                         //业务界面开发尽量放这
```

⚡️ 注意：

# 几个文件

- router/index.ts
    - identity 该字段配置 权限，前端卡权限或者后端卡权限皆可，具体看实际业务
    - hide 该字段配置， 是否显隐菜单

```javascript

示例代码

import { createRouter, createWebHashHistory, RouteRecordRaw } from "vue-router";
import Home from "../views/EntryMain.vue";
import { getToken, setCurSelect } from "@/utils";
import NProgress from "@/utils/progress";

export const routes: Array<RouteRecordRaw> = [
    {
        path: "/",
        name: "主包",
        component: Home,
        redirect: "/dashboard",
        children: [
            {
                path: "/about",
                name: "About",
                meta: {
                    title: "关于",
                    icon: "icon-link",
                    identity: ["admin", "user1"],
                    hide: true,
                },
                // route level code-splitting
                // this generates a separate chunk (about.[hash].js) for this route
                // which is lazy-loaded when the route is visited.
                component: () => import(/* webpackChunkName: "about" */ "../views/About.vue"),
            },
            {
                path: "/info",
                name: "info",
                redirect: "/infosystem",
                meta: {
                    title: "系统信息",
                    icon: "icon-apps",
                    identity: ["admin", "user1"],
                },
                component: () => import("../views/Pages/Infos/index.vue"),
                children: [
                    {
                        path: "infosystem",
                        name: "infosystem",
                        meta: {
                            title: "系统介绍",
                            officalTitle: "系统信息",
                            icon: "",
                        },
                        component: () => import("../views/Pages/Infos/InfoSystem/index.vue"),
                    },
                ],
            },
        ],
    },
    {
        path: "/login",
        name: "Login",
        meta: {
            title: "登录",
        },
        component: () => import("../views/Pages/Login/index.vue"),
    },
];

const router = createRouter({
    history: createWebHashHistory(process.env.BASE_URL),
    routes,
    /**业务路由可拆包放入**/
});

```

- utils/request.js
    - axios请求拦截器、响应拦截器简单配置

```javascript

示例代码

import axios from "axios";
import { getToken } from "./index";
import { Message } from "@arco-design/web-vue";

export let service = axios.create({
    baseURL: "/api",
    timeout: 30000,
});

service.interceptors.request.use(
    function (config) {
        // Do something before request is sent
        config.headers = {
            Authorization: getToken(),
        };
        return config;
    },
    function (error) {
        // Do something with request error
        return Promise.reject(error);
    }
);

// Add a response interceptor
service.interceptors.response.use(
    function (response) {
        // Any status code that lie within the range of 2xx cause this function to trigger
        // Do something with response data
        const { data, status } = response;
        if (status != 200 && data.code != 200) {
            Message.error("请求超时");
        } else return response;
    },
    function (error) {
        // Any status codes that falls outside the range of 2xx cause this function to trigger
        // Do something with response error
        Message.error("请求超时");
        return Promise.reject(error);
    }
);

```

- src/index.ts
    - 项目入口文件，此单页应用(SPA)的主入口
    - 引入UI库、Vuex、VueRouter、全局组件的注册

```javascript

示例代码

import { createApp } from "vue";
import App from "./App.vue";
import router from "./router";
import store from "./store";
import ArcoVue from "@arco-design/web-vue";
import ArcoVueIcon from "@arco-design/web-vue/es/icon";
import "@arco-design/web-vue/dist/arco.css";
import "animate.css";

const EZ = createApp(App);
//独立组件
import { install } from "@/components";

install.forEach((el) => {
    EZ.component(el.name, el);
});

EZ.use(store).use(router).use(ArcoVue).use(ArcoVueIcon).mount("#app");

```

- vue.config.js
    - vue中直接修改webpack配置的文件
    - 这里涉及到异构项目(前后分离),会涉及到跨域(协议、域名、端口任意一者不同导致),
        - 最简单解决方式就是让后端在响应头设置 Access-control-allow-origin = true
        - 这里采用前端的一种目前常用解决方式: 在vue.config.js中 proxy字段进行代理

```javascript

示例代码

module.exports = {
    devServer: {
        proxy: {
            '/api': {
                target: `http://${myHost}:3000`,
                changeOrigin: true,
            }
        }
    }
}

```