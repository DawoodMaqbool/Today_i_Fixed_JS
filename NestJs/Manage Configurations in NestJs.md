## Problem
If you want to do configuration management in NestJS, you can use the following document.

## Environment
Visual Studio Code, NodeJS, NestJs.

## How you fix it
Assuming that the setup is completed on your development environment. We first install the required @nestjs/config package. Then, we import the ConfigModule into our root module and use the forRoot() method to control its behavior. After that, we can access the configuration variables through the ConfigService.

## Solution
```
// Step 1: Install the required @nestjs/config package
// Using npm
// $ npm i --save @nestjs/config

// Step 2: Import the ConfigModule into your root module (e.g., app.module.ts)
import { Module } from '@nestjs/common';
import { ConfigModule } from '@nestjs/config';

@Module({
  imports: [ConfigModule.forRoot()],
})
export class AppModule {}

// Step 3: Access the configuration variables through the ConfigService
import { Injectable } from '@nestjs/common';
import { ConfigService } from '@nestjs/config';

@Injectable()
export class AppService {
  constructor(private configService: ConfigService) {}

  getDatabaseUrl(): string {
    return this.configService.get<string>('DATABASE_URL');
  }
}

```

This solution installs the @nestjs/config package, imports the ConfigModule into the root module, and accesses the configuration variables through the ConfigService. The get() method of ConfigService is used for reading these configuration variables1. Please replace 'DATABASE_URL' with your actual configuration variable key. For more advanced configuration management, you can make use of any configuration management library, such as dotenv, to implement a completely custom, production grade configuration management system.
