<h1 align="center">Notas de Estudo — product-catalog-api</h1>

<div align="center">

[![Repositório no GitHub](https://img.shields.io/badge/Código%20Fonte-GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/VictorMartinsD/product-catalog-api)
[![Product Specification](https://img.shields.io/badge/Product%20Specification-Documentation-0ea5e9?style=for-the-badge)](./product-spec.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](https://github.com/VictorMartinsD/product-catalog-api/blob/main/LICENSE)

</div>

<a id="sumario"></a>

<div align="center">

## Sumário | Summary

| Português                                                                                                                                                                                                                                                                                                                                                                      | English                                                                                                                                                                                                                                                                                                                                                              |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Visão Geral Técnica](#visao-geral-tecnica)<br>[Guia de Implementação](#guia-de-implementacao)<br>[Conceitos e Tecnologias](#conceitos-e-tecnologias)<br>[Decisões Técnicas](#decisoes-tecnicas)<br>[Problemas Resolvidos](#problemas-resolvidos)<br>[Boas Práticas](#boas-praticas)<br>[Aprendizados Técnicos](#aprendizados-tecnicos)<br>[Próximos Passos](#proximos-passos) | [Technical Overview](#technical-overview)<br>[Implementation Guide](#implementation-guide)<br>[Concepts and Technologies](#concepts-and-technologies)<br>[Technical Decisions](#technical-decisions)<br>[Resolved Issues](#resolved-issues)<br>[Best Practices](#best-practices)<br>[Technical Learnings](#technical-learnings)<br>[Next Steps](#next-steps-english) |

</div>

<a name="visao-geral-tecnica"></a>

## 📌 Visão Geral Técnica

O `product-catalog-api` organiza uma API HTTP em módulos TypeScript com responsabilidades separadas:

- `src/server.ts`: inicialização do Express, middleware global, tratamento de erros e porta `3333`.
- `src/routes/`: composição dos routers e definição dos endpoints.
- `src/controllers/`: processamento das requisições de produtos.
- `src/middleware/`: funções executadas durante o ciclo da requisição.
- `src/types/`: extensões de tipos usadas pela aplicação.
- `src/utils/`: representação de erros da aplicação.

O router principal monta `productsRoutes` sob o prefixo `/products`. O recurso expõe `GET /products/` para ler `page` e `limit`, e `POST /products/` para processar a criação de um produto.

<a name="guia-de-implementacao"></a>

## 🧭 Guia de Implementação

1. O servidor é inicializado com processamento JSON e o router principal.
2. O router principal registra as rotas de produtos sob `/products`.
3. O controller de produtos define as operações de consulta e criação.
4. O middleware específico é executado antes do controller no fluxo de criação.
5. O corpo da criação é validado antes de gerar a resposta.
6. O middleware global converte erros conhecidos e inesperados em respostas HTTP.
7. O script `npm run dev` executa `tsx watch src/server.ts` durante o desenvolvimento.

<a name="conceitos-e-tecnologias"></a>

## 🧠 Conceitos e Tecnologias

- **Express:** servidor HTTP, routers e middleware.
- **TypeScript:** tipagem estática com `strict` e `strictNullChecks` habilitados.
- **Zod:** schema executável para validar `name` e `price`.
- **Declaration merging:** extensão de `Express.Request` para representar `user_id`.
- **Módulos por responsabilidade:** separação entre bootstrap, rotas, controllers, middleware, tipos e utilitários.
- **Contrato de entrada:** `name` é texto com pelo menos três caracteres após `trim()`, e `price` é numérico e positivo.

<a name="decisoes-tecnicas"></a>

## ⚙️ Decisões Técnicas

- Rotas e controllers ficam separados para manter o bootstrap concentrado na inicialização.
- O middleware específico é aplicado antes da criação para preparar o contexto da requisição.
- A extensão de `Express.Request` evita representar `user_id` com `any` ou casts locais.
- A validação é feita antes do processamento do controller para impedir que entradas inválidas avancem.
- O tratamento de erros fica depois das rotas para centralizar as respostas de falha.
- `AppError` possui status padrão `400`, embora ainda não seja usado pelo controller de produtos.

<a name="problemas-resolvidos"></a>

## 🧩 Problemas Resolvidos

### Fluxo entre middleware e controller

- **Contexto:** o middleware adiciona `user_id` antes da criação.
- **Causa:** essa propriedade não existe no `Express.Request` padrão.
- **Solução:** a interface de requisição foi estendida em `src/types/request.d.ts`.
- **Aprendizado:** valores adicionados no pipeline precisam estar representados no contrato de tipos.

### Validação de dados externos

- **Contexto:** o corpo da requisição chega de fora da aplicação.
- **Causa:** dados ausentes, curtos ou não positivos não atendem ao contrato do produto.
- **Solução:** um schema Zod valida e descreve os problemas da entrada.
- **Aprendizado:** validação executável reduz ambiguidade entre o formato esperado e o formato recebido.

### Respostas de erro

- **Contexto:** falhas de validação e falhas internas precisam de respostas diferentes.
- **Causa:** cada tipo de erro possui um significado HTTP distinto.
- **Solução:** o middleware global trata `AppError`, `ZodError` e falhas inesperadas.
- **Aprendizado:** centralizar erros evita respostas inconsistentes entre endpoints.

<a name="boas-praticas"></a>

## 🛠️ Boas Práticas

- Separação de responsabilidades entre inicialização, rotas, controllers e middleware.
- Nomes de arquivos e módulos alinhados ao recurso de produtos.
- Uso de `strict` e `strictNullChecks` para verificação estática rigorosa.
- Validação explícita de dados externos antes do processamento.
- Mensagens de validação específicas para nome e preço.
- Respostas de erro centralizadas para manter um comportamento previsível.
- Uso de `next()` para manter o encadeamento do middleware do Express.

<a name="aprendizados-tecnicos"></a>

## 📚 Aprendizados Técnicos

O projeto reforçou como organizar uma API em camadas de entrada, roteamento, middleware e controller. Também consolidou a validação de dados com schemas, a extensão de tipos de bibliotecas externas e a criação de respostas de erro consistentes.

O principal aprendizado foi perceber que um backend pequeno já exige decisões explícitas sobre contrato de entrada, ordem do pipeline, tipagem e comunicação de falhas.

<a name="proximos-passos"></a>

## 🔄 Próximos Passos

- Adicionar testes para regras de nome, preço e respostas de erro.
- Introduzir persistência de produtos.
- Implementar consulta real com paginação.
- Adicionar operações de edição e remoção.
- Evoluir o identificador de contexto para autenticação real.

O código-fonte está disponível no [repositório de `product-catalog-api`](https://github.com/VictorMartinsD/product-catalog-api).

Notas de estudo técnico por [Victor Martins](https://github.com/VictorMartinsD).

---

<div align="center">

## ENGLISH VERSION

</div>

<a name="technical-overview"></a>

## 📌 Technical Overview

`product-catalog-api` organizes an HTTP API into TypeScript modules with separate responsibilities:

- `src/server.ts`: Express initialization, global middleware, error handling, and port `3333`.
- `src/routes/`: router composition and endpoint definitions.
- `src/controllers/`: product request processing.
- `src/middleware/`: functions executed during the request lifecycle.
- `src/types/`: application-specific type extensions.
- `src/utils/`: application error representation.

The main router mounts `productsRoutes` under `/products`. The resource exposes `GET /products/` to read `page` and `limit`, and `POST /products/` to process product creation.

<a name="implementation-guide"></a>

## 🧭 Implementation Guide

1. The server starts with JSON processing and the main router.
2. The main router registers product routes under `/products`.
3. The products controller defines query and creation operations.
4. Route-specific middleware runs before the controller in the creation flow.
5. The creation body is validated before the response is generated.
6. Global error middleware converts known and unexpected failures into HTTP responses.
7. The `npm run dev` script runs `tsx watch src/server.ts` during development.

<a name="concepts-and-technologies"></a>

## 🧠 Concepts and Technologies

- **Express:** HTTP server, routers, and middleware.
- **TypeScript:** static typing with `strict` and `strictNullChecks` enabled.
- **Zod:** executable schema for validating `name` and `price`.
- **Declaration merging:** extends `Express.Request` to represent `user_id`.
- **Responsibility-based modules:** separates bootstrap, routes, controllers, middleware, types, and utilities.
- **Input contract:** `name` is text with at least three characters after `trim()`, and `price` is numeric and positive.

<a name="technical-decisions"></a>

## ⚙️ Technical Decisions

- Routes and controllers are separated to keep the bootstrap focused on initialization.
- Route-specific middleware runs before creation to prepare request context.
- Extending `Express.Request` avoids representing `user_id` with `any` or local casts.
- Validation happens before controller processing so invalid input cannot proceed.
- Error handling is registered after the routes to centralize failure responses.
- `AppError` has a default status of `400`, although it is not yet used by the products controller.

<a name="resolved-issues"></a>

## 🧩 Resolved Issues

### Middleware and controller flow

- **Context:** middleware adds `user_id` before creation.
- **Cause:** the property does not exist on the default `Express.Request` type.
- **Solution:** the request interface was extended in `src/types/request.d.ts`.
- **Learning:** values added to the pipeline must be represented in the type contract.

### External data validation

- **Context:** the request body comes from outside the application.
- **Cause:** missing, short, or non-positive values do not meet the product contract.
- **Solution:** a Zod schema validates and describes input issues.
- **Learning:** executable validation reduces ambiguity between expected and received formats.

### Error responses

- **Context:** validation failures and internal failures need different responses.
- **Cause:** each error type has a distinct HTTP meaning.
- **Solution:** global middleware handles `AppError`, `ZodError`, and unexpected failures.
- **Learning:** centralized errors prevent inconsistent endpoint responses.

<a name="best-practices"></a>

## 🛠️ Best Practices

- Separate responsibilities between initialization, routes, controllers, and middleware.
- Use file and module names aligned with the product resource.
- Use `strict` and `strictNullChecks` for rigorous static checking.
- Validate external data explicitly before processing.
- Provide specific validation messages for name and price.
- Centralize error responses for predictable behavior.
- Use `next()` to preserve Express middleware chaining.

<a name="technical-learnings"></a>

## 📚 Technical Learnings

The project reinforced how to organize an API into input, routing, middleware, and controller layers. It also consolidated schema-based data validation, external-library type extension, and consistent error responses.

The main learning was that even a small backend requires explicit decisions about input contracts, pipeline order, typing, and failure communication.

<a name="next-steps-english"></a>

## 🔄 Next Steps

- Add tests for name, price, and error-response rules.
- Introduce product persistence.
- Implement a real paginated query.
- Add edit and delete operations.
- Evolve the request context identifier into real authentication.

The source code is available in the [ `product-catalog-api` repository](https://github.com/VictorMartinsD/product-catalog-api).

Technical study notes by [Victor Martins](https://github.com/VictorMartinsD).
