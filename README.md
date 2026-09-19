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

## Create Module, Service, and Controller
```bash
nest g module student
```
```bash
nest g controller student
```
```bash
nest g service student
```
---

### `student.service.ts`
```bash
import { Injectable } from '@nestjs/common';

@Injectable()
export class StudentService {
    private students = [
        { id: 1, name: 'Wasim', age: 29 },
        { id: 2, name: 'Hannan', age: 25 },
    ];

    async createStudent(data: {name: string, age: number}){
        const newStudent = {
            id: Date.now(),
            ...data,
        };
        this.students.push(newStudent);
        return newStudent;
    }

    async getAllStudents(){
        return this.students;
    }
}
```
---

### `student.controller.ts`
```bash
import { Body, Controller, Get, Post } from '@nestjs/common';
import { StudentService } from './student.service'

@Controller('student')
export class StudentController {
    constructor(private readonly studentService: StudentService){}

    @Get()
    async getAllStudents(){
        return this.studentService.getAllStudents();
    }

    @Post()
    async createStudent(@Body() data: {name: string, age: number}){
        return this.studentService.createStudent(data);
    }
}
```
---

#### `app.module.ts`
```bash
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { StudentModule } from './student/student.module';
import { LoggerModule } from 'nestjs-pino';

@Module({
  imports: [ LoggerModule.forRoot({
    pinoHttp: {
      level: process.env.NODE_ENV === 'production' ? 'info' : 'debug',
      transport: process.env.NODE_ENV !== 'production' ? { target: 'pino-pretty' } : undefined,
      redact: ['req.headers.authorization', 'req.headers.cookie'],
    },
  }), StudentModule],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```
---

#### `main.ts`
```bash
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import { Logger } from 'nestjs-pino';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.useLogger(app.get(Logger))
  await app.listen(process.env.PORT ?? 3000);
}
bootstrap();
```
---

>## OUTPUT
>#### project run korle
<img width="812" height="413" alt="image" src="https://github.com/user-attachments/assets/ec770cd8-d33c-4824-9b85-6a43b71d5ccb" />

>#### Post method er output
<img width="1305" height="408" alt="image" src="https://github.com/user-attachments/assets/1f50d13b-cb8f-4664-a9b0-22753e5e943c" />

<img width="1328" height="594" alt="image" src="https://github.com/user-attachments/assets/7952eede-089b-4ba5-bd1f-b446ff0228a7" />

>#### Get method er output
<img width="404" height="489" alt="image" src="https://github.com/user-attachments/assets/931ffd11-9ec0-4240-b5be-0cf6cc132000" />

<img width="437" height="550" alt="image" src="https://github.com/user-attachments/assets/8981709d-d538-4059-8ad5-9150d97d71e4" />

---
