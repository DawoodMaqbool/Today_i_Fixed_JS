## Problem
If you want to log HTTP requests in NestJS, you can use the following document.

## Environment
Visual Studio Code, NodeJS, NestJs.

## How you fix it
Assuming that the setup is completed on your development environment. We create a new middleware that logs the HTTP requests. Then, we apply this middleware in our main file.

## Solution
```
// Step 1: Create a new middleware file (e.g., logger.middleware.ts)
import { Injectable, NestMiddleware, Logger } from '@nestjs/common';
import { Request, Response, NextFunction } from 'express';

@Injectable()
export class LoggerMiddleware implements NestMiddleware {
  private logger = new Logger('HTTP');

  use(request: Request, response: Response, next: NextFunction): void {
    const { ip, method, originalUrl } = request;
    const userAgent = request.get('user-agent') || '';

    response.on('finish', () => {
      const { statusCode } = response;
      const contentLength = response.get('content-length');

      this.logger.log(
        `${method} ${originalUrl} ${statusCode} ${contentLength} - ${userAgent} ${ip}`
      );
    });

    next();
  }
}

// Step 2: Apply the middleware in your main file (e.g., main.ts)
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import { LoggerMiddleware } from './logger.middleware';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  app.use(LoggerMiddleware);

  await app.listen(3000);
}
bootstrap();

```

This solution creates a Logger middleware that logs the HTTP requests and applies it in the main file. The use method logs the HTTP method, URL, status code, content length, user agent, and IP address of each request. Please replace the console log statements with your own custom logic. For more advanced logging functionality, you can make use of any Node.js logging package, such as Winston, to implement a completely custom, production grade logging system1.
