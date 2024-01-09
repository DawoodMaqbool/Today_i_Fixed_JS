## Problem
If you want to integrate authentication in your NestJS application, you can follow the steps in this document.

## Environment
Visual Studio Code, NodeJS, NestJS, PassportJS.

## How you fix it
Assuming that the setup is completed on your development environment, we start by installing the necessary packages for authentication. In this scenario, we are using PassportJS, a popular authentication middleware for Node.js. Once we have installed the necessary packages, we then create an authentication module and service that uses PassportJS strategies for validating user credentials. Then we protect our routes using guards.

## Solution
```
const { Module } = require('@nestjs/common');
const { PassportModule } = require('@nestjs/passport');
const { LocalStrategy } = require('./local.strategy');
const { AuthService } = require('./auth.service');

@Module({
  imports: [PassportModule],
  providers: [AuthService, LocalStrategy],
})
class AuthModule {}

// auth.service.js
const { Injectable } = require('@nestjs/common');
const { UsersService } = require('../users/users.service');

@Injectable()
class AuthService {
  constructor(private usersService: UsersService) {}

  async validateUser(username: string, pass: string): Promise<any> {
    const user = await this.usersService.findOne(username);
    if (user && user.password === pass) {
      const { password, ...result } = user;
      return result;
    }
    return null;
  }
}

// local.strategy.js
const { Strategy } = require('passport-local');
const { PassportStrategy } = require('@nestjs/passport');
const { Injectable, UnauthorizedException } = require('@nestjs/common');
const { AuthService } = require('./auth.service');

@Injectable()
class LocalStrategy extends PassportStrategy(Strategy) {
  constructor(private authService: AuthService) {
    super();
  }

  async validate(username: string, password: string): Promise<any> {
    const user = await this.authService.validateUser(username, password);
    if (!user) {
      throw new UnauthorizedException();
    }
    return user;
  }
}

```

In the above code, we first define an AuthModule that imports PassportModule and provides AuthService and LocalStrategy. In AuthService, we define a validateUser method that checks if the user exists and if the password is correct. In LocalStrategy, we extend PassportStrategy and override the validate method to use our validateUser method from AuthService. If the user is not found or the password is incorrect, we throw an UnauthorizedException.

Please note that this is a basic example and real-world applications may require more complex setups, including hashing passwords, handling tokens, etc. Also, remember to protect your routes using guards. You can refer to the official NestJS documentation for more detailed information.
