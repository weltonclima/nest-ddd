# NestJS + DDD Simplificado — CRUD

Este projeto é um exemplo de aplicação NestJS utilizando uma estrutura simplificada baseada em DDD (Domain-Driven Design). Ele implementa um CRUD de usuários com separação clara entre entidade, serviço, repositório e controller.

---

## 🧱 Estrutura do Projeto

src/\
├── user/\
│ ├── user.entity.ts `# Entidade de domínio`\
│ ├── user.repository.ts `# Repositório (infraestrutura)`\
│ ├── user.service.ts `# Casos de uso (aplicação)`\
│ ├── user.controller.ts `# Entrada HTTP (interface)`\
│ ├── user.dto.ts `# DTOs (entrada de dados)`\
│ └── user.module.ts\
├── app.module.ts\
└── main.ts\

### 📌 Explicação

- **Entity**: Representa o objeto de domínio com regras de negócio internas (`user.entity.ts`)
- **Repository**: Camada que simula ou lida com o banco de dados (`user.repository.ts`)
- **Service**: Onde ficam os casos de uso e regras de negócio (como `createUser`, `findOne`)  
- **Controller**: Recebe as requisições HTTP e repassa para o serviço  
- **DTO**: Define o formato de entrada dos dados  

---

## 📦 Exemplos de Requisições


### 📌 Explicação

- **Entity**: Representa o objeto de domínio com regras de negócio internas (`user.entity.ts`)
- **Repository**: Camada que simula ou lida com o banco de dados (`user.repository.ts`)
- **Service**: Onde ficam os casos de uso e regras de negócio (como `createUser`, `findOne`)  
- **Controller**: Recebe as requisições HTTP e repassa para o serviço  
- **DTO**: Define o formato de entrada dos dados  

---

🧪 Exemplos de Código
📘 user.entity.ts


```
export class User {
  constructor(
    public readonly id: string,
    public name: string,
    public email: string,
  ) {}

  updateName(newName: string) {
    this.name = newName;
  }
}
```
💾 user.repository.ts

```
import { Injectable } from '@nestjs/common';
import { User } from './user.entity';

@Injectable()
export class UserRepository {
  private users: User[] = [];

  async save(user: User): Promise<User> {
    this.users.push(user);
    return user;
  }

  async findAll(): Promise<User[]> {
    return this.users;
  }

  async findById(id: string): Promise<User | undefined> {
    return this.users.find(user => user.id === id);
  }

  async delete(id: string): Promise<void> {
    this.users = this.users.filter(user => user.id !== id);
  }
}
```
🧠 user.service.ts

```
import { Injectable } from '@nestjs/common';
import { User } from './user.entity';
import { UserRepository } from './user.repository';

@Injectable()
export class UserService {
  constructor(private readonly userRepo: UserRepository) {}

  async create(name: string, email: string): Promise<User> {
    const user = new User(Date.now().toString(), name, email);
    return this.userRepo.save(user);
  }

  async findAll(): Promise<User[]> {
    return this.userRepo.findAll();
  }

  async findOne(id: string): Promise<User> {
    const user = await this.userRepo.findById(id);
    if (!user) throw new Error('User not found');
    return user;
  }

  async delete(id: string): Promise<void> {
    return this.userRepo.delete(id);
  }
}
```
📡 user.controller.ts

```
import { Controller, Get, Post, Body, Param, Delete } from '@nestjs/common';
import { UserService } from './user.service';
import { CreateUserDto } from './user.dto';

@Controller('users')
export class UserController {
  constructor(private readonly userService: UserService) {}

  @Post()
  create(@Body() body: CreateUserDto) {
    return this.userService.create(body.name, body.email);
  }

  @Get()
  findAll() {
    return this.userService.findAll();
  }

  @Get(':id')
  findOne(@Param('id') id: string) {
    return this.userService.findOne(id);
  }

  @Delete(':id')
  delete(@Param('id') id: string) {
    return this.userService.delete(id);
  }
}
```
📦 user.dto.ts

```
export class CreateUserDto {
  name: string;
  email: string;
}
```
