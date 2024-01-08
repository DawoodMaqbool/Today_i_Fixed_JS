## Problem
If you want to use role guards in NestJS for role-based access control, you can use the following document.

## Environment
Visual Studio Code, NodeJS, NestJs.

## How you fix it
Assuming that the setup is completed on your development environment. We first create a roles decorator to assign roles to the routes. Then, we create a roles guard that checks if the user has the necessary roles to access the route.

## Solution
```
// Step 1: Create a roles decorator (e.g., roles.decorator.ts)
import { SetMetadata } from '@nestjs/common';

export const Roles = (...roles: string[]) => SetMetadata('roles', roles);

// Step 2: Create a roles guard (e.g., roles.guard.ts)
import { Injectable, CanActivate, ExecutionContext } from '@nestjs/common';
import { Reflector } from '@nestjs/core';

@Injectable()
export class RolesGuard implements CanActivate {
  constructor(private reflector: Reflector) {}

  canActivate(context: ExecutionContext): boolean {
    const roles = this.reflector.get<string[]>('roles', context.getHandler());
    if (!roles) {
      return true;
    }

    const request = context.switchToHttp().getRequest();
    const user = request.user;
    const hasRole = () => user.roles.some((role) => roles.includes(role));

    return user && user.roles && hasRole();
  }
}

// Step 3: Use the roles decorator and guard in your controller (e.g., app.controller.ts)
import { Controller, Get, UseGuards, Request } from '@nestjs/common';
import { Roles } from './roles.decorator';
import { RolesGuard } from './roles.guard';

@Controller('users')
@UseGuards(RolesGuard)
export class UsersController {
  @Get()
  @Roles('admin')
  findAll(@Request() req): string {
    // This route is protected and only accessible for 'admin' users
    return 'This is a protected route';
  }
}

```

This solution creates a roles decorator and a roles guard, and uses them in a controller. The Roles decorator is used to assign roles to the routes. The RolesGuard checks if the user has the necessary roles to access the route. Please replace 'admin' with your actual roles. For more advanced role-based access control, you can make use of any access control library, such as AccessControl, to implement a completely custom, production grade role-based access control system.
