## Problem
If you want to integrate Sentry with NestJS for error tracking and monitoring, you can use the following document.

## Environment
Visual Studio Code, NodeJS, NestJs.

## How you fix it
Assuming that the setup is completed on your development environment. We first install the required Sentry Node SDK. Then, we initialize Sentry in our main file. After that, we create a new interceptor file and inject that interceptor to the desired controller.

## Solution
```
// Step 1: Install the required Sentry Node SDK
// Using yarn
// $ yarn add @sentry/node @sentry/tracing
// Or use npm
// $ npm install @sentry/node @sentry/tracing

// Step 2: Initialize Sentry in your main file (e.g., main.ts)
import * as Sentry from '@sentry/node';
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

Sentry.init({
  dsn: 'https://<your-sentry-dsn>@sentry.io/<your-project-id>',
});

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  await app.listen(3000);
}
bootstrap();

// Step 3: Create a new interceptor file (e.g., sentry.interceptor.ts)
import {
  CallHandler,
  ExecutionContext,
  Injectable,
  NestInterceptor,
} from '@nestjs/common';
import { Observable } from 'rxjs';
import { tap } from 'rxjs/operators';
import * as Sentry from '@sentry/node';

@Injectable()
export class SentryInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    return next
      .handle()
      .pipe(
        tap(null, (exception) => {
          Sentry.captureException(exception);
        }),
      );
  }
}

// Step 4: Inject the interceptor to the desired controller
import { Controller, Get, UseInterceptors } from '@nestjs/common';
import { SentryInterceptor } from './sentry.interceptor';

@Controller()
@UseInterceptors(SentryInterceptor)
export class AppController {
  @Get()
  getHello(): string {
    throw new Error('An error for testing Sentry');
  }
}

```
