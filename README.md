## Pino Logger

#### Install
```bash
npm install nestjs-pino pino-http pino-pretty
```
---

#### `app.module.ts`
```bash
import { LoggerModule } from 'nestjs-pino';

@Module({
  imports: [
    LoggerModule.forRoot({
      pinoHttp: {
        level: process.env.NODE_ENV === 'production' ? 'info' : 'debug',
        transport: process.env.NODE_ENV !== 'production' 
          ? { target: 'pino-pretty' } 
          : undefined,
        redact: ['req.headers.authorization', 'req.headers.cookie'],
      },
    }),
  ],
})
export class AppModule {}
```
---

#### `main.ts`
```bash
app.useLogger(app.get(Logger));
```
---
