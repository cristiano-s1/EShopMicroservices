# .NET 8 Microservices

Projeto desenvolvido com o objetivo de estudar e aplicar conceitos de **arquitetura de microserviços utilizando .NET 8**, seguindo como referência o curso [Microservices Architecture and Implementation on .NET](https://www.udemy.com/course/microservices-architecture-and-implementation-on-dotnet/), de Mehmet Ozkaya.

O projeto simula uma plataforma de **e-commerce distribuída**, composta por diferentes microserviços responsáveis por catálogo de produtos, carrinho de compras, descontos e processamento de pedidos.

O foco principal está na aplicação prática de conceitos de **Microservices Architecture, DDD, CQRS, Vertical Slice Architecture, Clean Architecture, comunicação síncrona e assíncrona e containerização**.

---

## 🏗️ Arquitetura

A solução é composta por múltiplos microserviços independentes, cada um responsável por um contexto específico do domínio.

```text
                         ┌──────────────────┐
                         │   Shopping Web   │
                         └────────┬─────────┘
                                  │
                                  ▼
                         ┌──────────────────┐
                         │   YARP Gateway   │
                         └────────┬─────────┘
                                  │
              ┌───────────────────┼───────────────────┐
              │                   │                   │
              ▼                   ▼                   ▼
       ┌─────────────┐     ┌─────────────┐    ┌─────────────┐
       │ Catalog API │     │  Basket API  │    │ Discount API│
       └──────┬──────┘     └──────┬──────┘    └──────┬──────┘
              │                   │                   │
              ▼                   ▼                   │
        PostgreSQL              Redis                 │
                                  │                   │
                                  └─────────┬─────────┘
                                            │
                                            ▼
                                     ┌─────────────┐
                                     │   Ordering  │
                                     │     API     │
                                     └──────┬──────┘
                                            │
                                            ▼
                                      ┌───────────┐
                                      │ RabbitMQ  │
                                      └─────┬─────┘
                                            │
                                            ▼
                                      Event-driven
                                      communication
```

### Microserviços

- **Catalog API** — gerenciamento de produtos e categorias.
- **Basket API** — gerenciamento do carrinho de compras.
- **Discount API** — cálculo e gerenciamento de descontos.
- **Ordering API** — processamento dos pedidos.
- **API Gateway** — entrada centralizada utilizando YARP.
- **Shopping Web** — aplicação web para interação com os serviços.

---

## 🚀 Tecnologias

### Backend

- C#
- .NET 8
- ASP.NET Core
- ASP.NET Core Minimal APIs
- Entity Framework Core
- MediatR
- FluentValidation
- Mapster
- Carter
- Refit

### Arquitetura e Design

- Microservices Architecture
- Domain-Driven Design (DDD)
- CQRS
- Vertical Slice Architecture
- Clean Architecture
- SOLID
- Dependency Injection
- Repository Pattern
- Mediator Pattern
- Proxy Pattern
- Decorator Pattern
- Cache-Aside Pattern
- Database per Service
- Event-Driven Architecture

### Comunicação

- REST APIs
- gRPC
- RabbitMQ
- MassTransit

### Bancos de dados e armazenamento

- SQL Server
- PostgreSQL
- Redis
- Entity Framework Core
- Marten

### Infraestrutura

- Docker
- Docker Compose
- YARP Reverse Proxy

### Observabilidade e qualidade

- Health Checks
- Logging
- Global Exception Handling
- Rate Limiting

---

## 📚 Principais conceitos praticados

### Microservices

Aplicação do conceito de serviços independentes, com responsabilidades bem definidas e comunicação através de APIs e mensagens.

Cada serviço possui seu próprio contexto e gerenciamento de dados, seguindo o padrão **Database per Service**.

### Domain-Driven Design

Aplicação de conceitos de DDD, incluindo:

- Entities
- Value Objects
- Aggregates
- Aggregate Roots
- Bounded Contexts
- Domain modeling

### CQRS

Separação das operações de **Commands** e **Queries**, utilizando MediatR para implementar o padrão Mediator.

```text
Command
   │
   ▼
Handler
   │
   ▼
Domain
   │
   ▼
Persistence
```

E para consultas:

```text
Query
   │
   ▼
Handler
   │
   ▼
Database
   │
   ▼
Response
```

### Vertical Slice Architecture

Organização do código por **feature/use case**, em vez de separar exclusivamente por camadas técnicas.

Exemplo:

```text
Features
├── CreateProduct
│   ├── CreateProductCommand.cs
│   ├── CreateProductHandler.cs
│   ├── CreateProductEndpoint.cs
│   └── CreateProductValidator.cs
│
├── GetProducts
│   ├── GetProductsQuery.cs
│   ├── GetProductsHandler.cs
│   └── GetProductsEndpoint.cs
│
└── DeleteProduct
    ├── DeleteProductCommand.cs
    ├── DeleteProductHandler.cs
    └── DeleteProductEndpoint.cs
```

Essa abordagem mantém as funcionalidades relacionadas próximas umas das outras e reduz o acoplamento entre features.

---

## 🔄 Comunicação entre Microserviços

O projeto utiliza diferentes estratégias de comunicação.

### Comunicação síncrona

Para cenários que exigem uma resposta imediata, são utilizadas APIs REST e **gRPC**.

```text
Basket API
     │
     │ gRPC
     ▼
Discount API
     │
     ▼
Discount Value
```

### Comunicação assíncrona

Para eventos de negócio, o projeto utiliza **RabbitMQ + MassTransit**.

```text
Basket API
    │
    │ BasketCheckout Event
    ▼
RabbitMQ
    │
    ├───────────────► Ordering API
    │
    └───────────────► Outros consumidores
```

Essa abordagem reduz o acoplamento entre os serviços e permite processamento assíncrono baseado em eventos.

---

## 🌐 API Gateway

O projeto utiliza **YARP (Yet Another Reverse Proxy)** como API Gateway.

Responsabilidades:

- Routing
- Reverse Proxy
- Centralização dos endpoints
- Transformação de requests
- Rate Limiting
- Comunicação com os microserviços

Fluxo:

```text
Client
  │
  ▼
YARP API Gateway
  │
  ├──► Catalog API
  ├──► Basket API
  ├──► Discount API
  └──► Ordering API
```

---

## 🗄️ Persistência

O projeto utiliza diferentes tecnologias de persistência de acordo com as necessidades de cada serviço.

| Serviço | Tecnologia |
|---|---|
| Catalog | PostgreSQL + Marten |
| Basket | Redis |
| Discount | SQL Server + Entity Framework Core |
| Ordering | SQL Server + Entity Framework Core |

Essa abordagem demonstra o conceito de **Polyglot Persistence**, permitindo escolher a tecnologia mais adequada para cada contexto.

---

## 🐳 Docker

Os serviços podem ser executados utilizando containers Docker.

O `docker-compose` é utilizado para orquestrar os componentes da aplicação, incluindo:

- APIs
- Databases
- Redis
- RabbitMQ
- API Gateway

Exemplo:

```bash
docker compose up -d
```

Para verificar os containers:

```bash
docker ps
```

Para interromper o ambiente:

```bash
docker compose down
```

---

## 📁 Estrutura da solução

Uma representação simplificada da solução:

```text
src/
│
├── Services/
│   ├── Catalog/
│   │   └── Catalog.API
│   │
│   ├── Basket/
│   │   └── Basket.API
│   │
│   ├── Discount/
│   │   └── Discount.Grpc
│   │
│   └── Ordering/
│       └── Ordering.API
│
├── ApiGateways/
│   └── YarpApiGateway
│
└── WebApp/
    └── Shopping.Web
│
└── docker-compose.yml
```

A estrutura pode variar de acordo com a evolução do projeto e adaptações realizadas durante os estudos.

---

## 🔍 Principais aprendizados

Durante o desenvolvimento deste projeto foram praticados conceitos como:

- Desenvolvimento de APIs com ASP.NET Core 8
- Minimal APIs
- Arquitetura de Microserviços
- Domain-Driven Design
- CQRS
- Vertical Slice Architecture
- Clean Architecture
- Comunicação REST
- Comunicação gRPC
- Comunicação assíncrona
- RabbitMQ
- MassTransit
- API Gateway
- YARP
- Redis
- PostgreSQL
- SQL Server
- Entity Framework Core
- Docker e Docker Compose
- Health Checks
- Exception Handling
- Logging
- Rate Limiting
- Design Patterns
- Dependency Injection

---

## 🎯 Objetivo do projeto

Este projeto foi desenvolvido como parte dos meus estudos em **Arquitetura de Software e desenvolvimento backend com .NET**, buscando aprofundar conhecimentos em sistemas distribuídos e arquiteturas modernas.

Além de acompanhar o conteúdo do curso, o projeto serve como laboratório para experimentar conceitos que podem ser aplicados em sistemas reais de alta complexidade.

---

## 📖 Referência

Projeto desenvolvido com base no curso:

**Microservices Architecture and Implementation on .NET**

Instrutor: **Mehmet Ozkaya**

O curso aborda a construção de uma aplicação de microserviços utilizando .NET 8, incluindo DDD, CQRS, Vertical Slice Architecture, RabbitMQ, MassTransit, gRPC, YARP, Redis, PostgreSQL, SQL Server e Docker.

🔗 [Curso na Udemy](https://www.udemy.com/course/microservices-architecture-and-implementation-on-dotnet/)

---

## 👨‍💻 Autor

**Cristiano**

Software Engineer | .NET | ASP.NET Core | Microservices

Projeto desenvolvido para fins de **estudo, prática e demonstração de conhecimentos em arquitetura de software e desenvolvimento de sistemas distribuídos**.
