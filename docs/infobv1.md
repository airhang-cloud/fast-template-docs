# 代码目录

```javascript
└─mock                               //mock模拟数据
├─app.controller.ts                  //配置路由
├─app.module.ts                      //controller 和 service要注入的文件
├─app.service.ts                     //服务，提供数据方法
├─main.ts                            //项目的主入口
```

⚡️ 注意：

# 几个文件

- src/app.controller.ts

```javascript

示例代码

import { Body, Controller, Get, Post } from '@nestjs/common';
import { AppService } from './app.service';   //导入service层的 文件

@Controller('/api')
export class AppController {
  constructor(private readonly appService: AppService) {}

  @Get()                              //路由---Get接口  /api
  getHello(): string {
    return this.appService.getHello();
  }

  @Post('/login')                     //路由---Post接口 /api/login
  responseInfo(@Body() body: any): any {
    const { user, psw } = body;
    return {
        msg: '登录成功',
        code: 403,
        ...this.appService.getUserInfo()  //this指向当前对象
    };
  }
}

```

- src/app.controller.ts

```javascript

示例代码

import { Injectable } from '@nestjs/common';
import { data } from './mock/index';
@Injectable()
export class AppService {
  getHello(): string {
    return 'Hello World!';
  }

  getUserInfo(): any {
    return data;
  }
}

```
