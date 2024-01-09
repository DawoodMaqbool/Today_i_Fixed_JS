## Problem
If you want to use WebSockets in NestJS, you can use the following document.


## Environment
Visual Studio Code, NodeJS, NestJs.

## How you fix it
Assuming that the setup is completed on your development environment. We create a new gateway that implements the OnGatewayInit, OnGatewayConnection, and OnGatewayDisconnect interfaces from @nestjs/websockets. This gateway will contain our custom logic for handling WebSocket connections and messages. Then, we provide this gateway in our root module so that NestJS can inject it wherever it’s needed.

## Solution
```
import { SubscribeMessage, WebSocketGateway, OnGatewayInit, OnGatewayConnection, OnGatewayDisconnect, WebSocketServer } from '@nestjs/websockets';
import { Logger } from '@nestjs/common';
import { Socket, Server } from 'socket.io';

@WebSocketGateway()
export class AppGateway implements OnGatewayInit, OnGatewayConnection, OnGatewayDisconnect {

  @WebSocketServer() server: Server;

  private logger: Logger = new Logger('AppGateway');

  @SubscribeMessage('msgToServer')
  handleMessage(client: Socket, text: string): void {
    this.server.emit('msgToClient', text);
  }

  afterInit(server: Server) {
    this.logger.log('Initialized!');
  }

  handleDisconnect(client: Socket) {
    this.logger.log(`Client disconnected: ${client.id}`);
  }

  handleConnection(client: Socket, ...args: any[]) {
    this.logger.log(`Client connected: ${client.id}`);
  }
}

```
