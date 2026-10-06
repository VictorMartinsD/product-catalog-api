<h1 align="center">Especificação de Produto — product-catalog-api</h1>

<div align="center">

[![Repositório no GitHub](https://img.shields.io/badge/Código%20Fonte-GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/VictorMartinsD/product-catalog-api)
[![📘 Notas de Estudo](https://img.shields.io/badge/%F0%9F%93%98%20Notas%20de%20Estudo-Documenta%C3%A7%C3%A3o-0ea5e9?style=for-the-badge)](./study-notes.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](https://github.com/VictorMartinsD/product-catalog-api/blob/main/LICENSE)

</div>

## Sumário | Summary

<div align="center">

| Português                                            | English                                         |
| ---------------------------------------------------- | ----------------------------------------------- |
| [Visão Geral do Produto](#1--visão-geral-do-produto) | [Product Overview](#1--product-overview)        |
| [Problema](#2--problema)                             | [Problem](#2--problem)                          |
| [Objetivo do Produto](#3--objetivo-do-produto)       | [Product Objective](#3--product-objective)      |
| [Usuário-Alvo](#4--usuário-alvo)                     | [Target User](#4--target-user)                  |
| [Funcionalidades](#5--funcionalidades)               | [Features](#5--features)                        |
| [Regras de Negócio](#6--regras-de-negócio)           | [Business Rules](#6--business-rules)            |
| [Fluxo do Usuário](#7--fluxo-do-usuário)             | [User Flow](#7--user-flow)                      |
| [MVP](#8--mvp-minimum-viable-product)                | [MVP](#8--mvp-minimum-viable-product-1)         |
| [Decisões de Produto](#9--decisões-de-produto)       | [Product Decisions](#9--product-decisions)      |
| [Limitações Atuais](#10--limitações-atuais)          | [Current Limitations](#10--current-limitations) |
| [Próximos Passos](#11--próximos-passos-roadmap)      | [Next Steps](#11--next-steps-roadmap)           |
| [Métricas de Sucesso](#12--métricas-de-sucesso)      | [Success Metrics](#12--success-metrics)         |

</div>

## 1 — Visão Geral do Produto

O `product-catalog-api` oferece uma interface para enviar dados básicos de produtos e receber uma resposta padronizada sobre o processamento. Também permite consultar uma indicação de página a partir dos parâmetros informados pelo usuário.

O produto atende ao estágio inicial de um catálogo: estabelece quais dados mínimos um produto deve possuir e impede o recebimento de nomes ou preços inválidos. O valor entregue está na previsibilidade da entrada e do retorno durante uma integração ou validação inicial de cadastro.

## 2 — Problema

Antes do uso do sistema, um consumidor da API não teria um ponto definido para encaminhar dados básicos de produtos nem regras explícitas para aceitar ou rejeitar esses dados.

As principais dificuldades tratadas são:

- Enviar um produto sem saber quais informações são obrigatórias.
- Aceitar nomes muito curtos ou formados apenas por espaços.
- Aceitar preços iguais ou menores que zero.
- Obter uma resposta consistente quando os dados não atendem às regras.

O problema precisa ser resolvido para que o cadastro inicial tenha um contrato funcional mínimo, mesmo antes da existência de um catálogo persistente.

## 3 — Objetivo do Produto

O produto pretende permitir que o usuário:

- Envie um produto com nome e preço.
- Saiba imediatamente se os dados atendem às regras mínimas.
- Receba os dados aceitos em uma resposta de criação.
- Informe uma página e um limite para obter a indicação correspondente no fluxo de consulta.

O resultado é uma base funcional para validação e integração de produtos, sem afirmar que o sistema já gerencia um catálogo completo.

## 4 — Usuário-Alvo

O usuário-alvo principal é o desenvolvedor que integra ou testa um serviço de catálogo de produtos.

Esse usuário:

- Possui conhecimento básico ou intermediário sobre requisições HTTP e dados estruturados.
- Precisa enviar informações de produtos para um serviço com regras previsíveis.
- Está construindo, validando ou estudando um fluxo inicial de cadastro.

O produto não é direcionado diretamente ao consumidor final que navega por uma loja, pois não oferece uma interface de compra ou de visualização de catálogo.

## 5 — Funcionalidades

### Consulta de página

- O usuário informa `page` e `limit` como parâmetros de consulta.
- O sistema retorna uma mensagem com os valores recebidos.

Essa funcionalidade representa uma consulta inicial de paginação, mas não retorna produtos armazenados.

### Envio de produto

- O usuário envia `name` e `price`.
- O sistema verifica se os dois dados atendem aos requisitos mínimos.
- Quando a entrada é válida, o sistema retorna os dados do produto e um identificador associado ao contexto da requisição.

### Retorno de validação

- Quando os dados são inválidos, o sistema informa que ocorreu um erro de validação.
- A resposta apresenta os problemas identificados para que o consumidor possa corrigir a entrada.

## 6 — Regras de Negócio

- O nome do produto é obrigatório.
- O nome do produto deve ser um texto.
- Espaços no início e no fim do nome não fazem parte do valor considerado.
- O nome do produto deve conter pelo menos três caracteres após a remoção desses espaços.
- O preço do produto é obrigatório.
- O preço deve ser numérico.
- O preço deve ser maior que zero.
- Uma entrada inválida não é aceita como produto criado.
- Uma criação válida retorna status de criação e os dados aceitos.
- A consulta de página apresenta os valores de `page` e `limit` recebidos, sem indicar que os produtos foram buscados ou persistidos.

## 7 — Fluxo do Usuário

### Fluxo de criação

1. O usuário envia o nome e o preço do produto.
2. O sistema verifica a presença e o formato dos dados.
3. O sistema rejeita a solicitação se o nome ou o preço não atender às regras.
4. O sistema informa os problemas de validação para permitir a correção.
5. Se os dados forem válidos, o sistema confirma a criação e retorna os valores aceitos.

### Fluxo de consulta de página

1. O usuário informa uma página e um limite.
2. O sistema recebe os valores enviados.
3. O sistema retorna uma indicação com esses valores.

## 8 — MVP (Minimum Viable Product)

O núcleo indispensável do produto é o envio de um produto com validação mínima de nome e preço.

Sem essa capacidade, o sistema deixa de cumprir sua função principal de oferecer um ponto controlado para a entrada inicial de produtos. A consulta de página complementa o escopo, mas ainda não representa uma consulta real a um catálogo.

## 9 — Decisões de Produto

- Exigir nome e preço reduz a quantidade de produtos incompletos recebidos pelo sistema.
- Exigir pelo menos três caracteres evita nomes vazios, curtos demais ou pouco identificáveis.
- Remover espaços excedentes do nome melhora a consistência do dado recebido.
- Aceitar somente preços positivos impede valores que não representam um preço válido para um produto.
- Retornar os problemas de validação reduz o retrabalho do consumidor da API, pois ele consegue identificar o que precisa corrigir.
- Responder com os dados aceitos torna o resultado da criação verificável para o usuário que está integrando o serviço.
- Manter o escopo sem persistência torna possível validar o contrato inicial do produto antes de introduzir operações de armazenamento e gerenciamento completo.

## 10 — Limitações Atuais

- Não há persistência de produtos.
- Não é possível consultar uma lista real de produtos.
- Não há edição de produtos.
- Não há remoção de produtos.
- Não há identificação permanente de produtos.
- Não há autenticação de usuários.
- Não há suporte a múltiplos contextos de usuário.
- A consulta de página não filtra nem pagina dados armazenados.
- Não há interface visual para usuários finais.
- Não há regras para categorias, estoque, descrição ou outros atributos de produto.

## 11 — Próximos Passos (Roadmap)

- Adicionar persistência dos produtos criados.
- Implementar consulta real do catálogo com paginação.
- Permitir edição e remoção de produtos.
- Definir identificadores permanentes para os produtos.
- Adicionar autenticação e controle de acesso.
- Incluir atributos como categoria, descrição e estoque.
- Criar respostas de consulta que diferenciem catálogo vazio, produto inexistente e consulta bem-sucedida.
- Disponibilizar uma interface para operação do catálogo por usuários não técnicos.

## 12 — Métricas de Sucesso

O sucesso do produto pode ser avaliado por meio de comportamentos observáveis:

- O usuário envia produtos válidos sem precisar repetir a solicitação por erro de formato.
- O usuário corrige uma entrada inválida a partir dos problemas informados pelo sistema.
- A criação válida retorna uma confirmação compreensível e os dados esperados.
- O consumidor da API consegue concluir o fluxo inicial de cadastro sem ambiguidade sobre os campos obrigatórios.
- O número de solicitações rejeitadas por nome ou preço inválido diminui após a correção das entradas.
- O fluxo de consulta de página retorna os valores solicitados de forma consistente.

O documento não estabelece metas numéricas porque ainda não existem dados de uso suficientes para definir uma linha de base confiável.

O código-fonte está disponível no [repositório de `product-catalog-api`](https://github.com/VictorMartinsD/product-catalog-api).

Documento de produto elaborado por [Victor Martins](https://github.com/VictorMartinsD).

Este documento descreve a visão funcional e estratégica do sistema.

---

<h1 align="center">Product Specification — product-catalog-api</h1>

## 1 — Product Overview

`product-catalog-api` provides an interface for submitting basic product data and receiving a standardized response about the processing result. It also allows a user to submit page parameters and receive an indication based on those values.

The product addresses the initial stage of a catalog: it defines the minimum data a product must contain and rejects names or prices that do not meet the basic rules. The delivered value is predictable input and output behavior during an integration or an initial product-registration flow.

## 2 — Problem

Before using the system, an API consumer would not have a defined point for submitting basic product data or explicit rules for accepting or rejecting that data.

The main difficulties addressed are:

- Submitting a product without knowing which information is required.
- Accepting names that are too short or contain only spaces.
- Accepting prices equal to or below zero.
- Receiving a consistent response when the data does not meet the rules.

This problem needs to be addressed so that the initial registration flow has a minimum functional contract, even before a persistent catalog exists.

## 3 — Product Objective

The product is intended to allow the user to:

- Submit a product with a name and price.
- Immediately know whether the data meets the minimum rules.
- Receive the accepted product data in a creation response.
- Provide a page and limit to receive the corresponding indication in the query flow.

The result is a functional foundation for product validation and integration, without claiming that the system already manages a complete catalog.

## 4 — Target User

The primary target user is a developer integrating with or testing a product catalog service.

This user:

- Has basic or intermediate knowledge of HTTP requests and structured data.
- Needs to submit product information to a service with predictable rules.
- Is building, validating, or studying an initial registration flow.

The product is not directly aimed at end consumers browsing a store because it does not provide a shopping or catalog-browsing interface.

## 5 — Features

### Page query

- The user provides `page` and `limit` as query parameters.
- The system returns a message containing the received values.

This feature represents an initial pagination query, but it does not return stored products.

### Product submission

- The user submits `name` and `price`.
- The system checks whether both values meet the minimum requirements.
- When the input is valid, the system returns the product data and an identifier associated with the request context.

### Validation response

- When the data is invalid, the system indicates that a validation error occurred.
- The response presents the identified issues so the consumer can correct the input.

## 6 — Business Rules

- The product name is required.
- The product name must be text.
- Leading and trailing spaces are not part of the considered value.
- The product name must contain at least three characters after those spaces are removed.
- The product price is required.
- The price must be numeric.
- The price must be greater than zero.
- Invalid input is not accepted as a created product.
- A valid creation returns a creation status and the accepted data.
- The page query displays the received `page` and `limit` values without indicating that products were fetched or persisted.

## 7 — User Flow

### Creation flow

1. The user submits the product name and price.
2. The system checks the presence and format of the data.
3. The system rejects the request if the name or price does not meet the rules.
4. The system reports the validation issues so the user can correct them.
5. If the data is valid, the system confirms the creation and returns the accepted values.

### Page query flow

1. The user provides a page and a limit.
2. The system receives the submitted values.
3. The system returns an indication containing those values.

## 8 — MVP (Minimum Viable Product)

The indispensable core of the product is submitting a product with minimum validation for its name and price.

Without this capability, the system would no longer fulfill its primary role of providing a controlled entry point for initial product data. The page query complements the scope, but it does not yet represent a real catalog query.

## 9 — Product Decisions

- Requiring a name and price reduces the number of incomplete products received by the system.
- Requiring at least three characters prevents empty, excessively short, or poorly identifiable names.
- Removing extra spaces from the name improves the consistency of the received data.
- Accepting only positive prices prevents values that do not represent a valid product price.
- Returning validation issues reduces rework for the API consumer because it identifies what must be corrected.
- Returning the accepted data makes the creation result verifiable for the user integrating the service.
- Keeping the scope free of persistence makes it possible to validate the initial product contract before introducing storage and complete catalog-management operations.

## 10 — Current Limitations

- Products are not persisted.
- A real product list cannot be queried.
- Products cannot be edited.
- Products cannot be removed.
- Products do not have permanent identifiers.
- Users are not authenticated.
- Multiple user contexts are not supported.
- The page query does not filter or paginate stored data.
- There is no visual interface for end users.
- There are no rules for categories, stock, descriptions, or other product attributes.

## 11 — Next Steps (Roadmap)

- Add persistence for created products.
- Implement a real catalog query with pagination.
- Allow products to be edited and removed.
- Define permanent product identifiers.
- Add authentication and access control.
- Add attributes such as category, description, and stock.
- Provide query responses that distinguish an empty catalog, a missing product, and a successful query.
- Provide an interface for non-technical users to operate the catalog.

## 12 — Success Metrics

Product success can be evaluated through observable behaviors:

- The user submits valid products without repeating the request because of format errors.
- The user corrects invalid input based on the issues reported by the system.
- A valid creation returns an understandable confirmation and the expected data.
- The API consumer completes the initial registration flow without ambiguity about required fields.
- The number of requests rejected for invalid names or prices decreases after input corrections.
- The page query flow consistently returns the requested values.

This document does not establish numerical targets because there is not yet enough usage data to define a reliable baseline.

The source code is available in the [ `product-catalog-api` repository](https://github.com/VictorMartinsD/product-catalog-api).

---

Product document prepared by [Victor Martins](https://github.com/VictorMartinsD).

This document describes the functional and strategic vision of the system.
