## Problem

If you want to integrate a custom logger with NestJS, you can use the following document.


## Environment
 Visual Studio Code, NodeJS, NestJs 

## How you fix it
  Assuming that the setup is completed on your development environment. We create a new service that implements the `LoggerService` interface from `@nestjs/common`. This service will contain our custom logic for logging. Then, we provide this service in our root module so that NestJS can inject it wherever it’s needed. Finally, we tell NestJS to use our custom logger service instead of the default one.


## Solution
```
import { LoggerService, Injectable } from '@nestjs/common';

@Injectable()
export class MyLoggerService implements LoggerService {
  log(message: string) {
    // Add your custom logic here
    console.log(message);
  }

  error(message: string, trace: string) {
    // Add your custom logic here
    console.error(message, trace);
  }

  warn(message: string) {
    // Add your custom logic here
    console.warn(message);
  }

  debug(message: string) {
    // Add your custom logic here
    console.debug(message);
  }

  verbose(message: string) {
    // Add your custom logic here
    console.log(message);
  }
}

// In your root module
import { Module } from '@nestjs/common';
import { MyLoggerService } from './my-logger.service';

@Module({
  providers: [
    {
      provide: MyLoggerService,
      useClass: MyLoggerService,
    },
  ],
  exports: [MyLoggerService],
})
export class AppModule {}

// In your main.ts file
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import { MyLoggerService } from './my-logger.service';

async function bootstrap() {
  const app = await NestFactory.create(AppModule, {
    logger: false,
  });
  app.useLogger(app.get(MyLoggerService));
  await app.listen(3000);
}
bootstrap();

```
