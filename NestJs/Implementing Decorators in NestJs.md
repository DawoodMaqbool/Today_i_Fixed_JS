## Problem
If you want to implement and use decorators in NestJS, you can use the following document.

## Environment
Visual Studio Code, NodeJS, NestJs.

## How you fix it
Assuming that the setup is completed on your development environment. We first create a custom decorator using the createParamDecorator function from @nestjs/common. Then, we use this decorator in our controller or service.

## Solution
```
// Step 1: Create a custom decorator (e.g., user.decorator.ts)
import { createParamDecorator, ExecutionContext } from '@nestjs/common';

export const User = createParamDecorator(
  (data: unknown, ctx: ExecutionContext) => {
    const request = ctx.switchToHttp().getRequest();
    return request.user;
  },
);

// Step 2: Use the custom decorator in your controller or service (e.g., app.controller.ts)
import { Controller, Get } from '@nestjs/common';
import { User } from './user.decorator';

@Controller()
export class AppController {
  @Get()
  findOne(@User() user: any) {
    console.log(user);
  }
}

```

This solution creates a User decorator and uses it in a controller. The User decorator extracts the user property from the request object. Please replace console.log(user); with your own custom logic. For more advanced decorator functionality, you can make use of any decorator feature, such as method decorators, property decorators, and class decorators, to implement a completely custom, production grade decorator system.
