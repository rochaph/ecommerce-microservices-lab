# ecommerce-microservices-lab

Laboratório de arquitetura distribuída: um e-commerce simplificado construído para praticar, de ponta a ponta,
**microsserviços, Arquitetura Hexagonal, mensageria, Session, OAuth 2.0/OIDC, Kubernetes, serverless e AWS**.

O domínio é propositalmente simples. O foco está em como os serviços se comunicam, falham, se recuperam,
autenticam usuários e são implantados.

## Arquitetura

```
                 Browser
                    │
             [Ingress NGINX]
                    │
        ┌───────────▼───────────┐       ┌───────────┐
        │   BFF / Gateway       │◄─────►│ Keycloak  │  OAuth 2.0 / OIDC
        │   sessão em Redis     │       └───────────┘
        └──────┬─────────┬──────┘
          REST │         │ REST (access token)
        ┌──────▼──┐  ┌───▼──────┐
        │  users  │  │  orders  │──► Postgres + Outbox
        └─────────┘  └───┬──────┘
                         │ eventos (Kafka)
              ┌──────────┼───────────┐
        ┌─────▼─────┐ ┌──▼────────┐  │   Saga: pedido → pagamento → estoque
        │ payments  │ │ inventory │  │
        └───────────┘ └───────────┘  │
                                     ▼  order.completed
                           SQS (LocalStack → AWS)
                                     │
                          ┌──────────▼──────────┐
                          │  notifications (Go) │  Lambda OU pod k8s
                          └─────────────────────┘
```

## Stack

| Camada | Tecnologia |
|---|---|
| Serviços principais | Java 21, Spring Boot 4 |
| Serviço serverless | Go (AWS Lambda) |
| Identidade | Keycloak (local), Amazon Cognito (AWS) |
| Sessão | Spring Session + Redis |
| Mensageria | Kafka, Amazon SQS |
| Persistência | PostgreSQL, DynamoDB |
| Orquestração | Kubernetes (kind), Helm, Argo CD |
| Observabilidade | OpenTelemetry, Prometheus, Grafana, Jaeger |
| Infraestrutura como código | Terraform (LocalStack e AWS) |
| CI | GitHub Actions |

## Onde cada conceito aparece

| Conceito | Onde |
|---|---|
| Microsserviços | 6 serviços, banco por serviço, deploy independente |
| Arquitetura Hexagonal | todos os serviços; `orders` é a referência; `notifications` troca adaptadores (Lambda ↔ HTTP) |
| Mensageria | Outbox, consumidor idempotente, retry + DLQ, Saga |
| Session | BFF com cookie de sessão e store no Redis |
| OAuth 2.0 / OIDC | Authorization Code + PKCE, Client Credentials, Token Exchange, validação de JWT |
| Kubernetes | kind, Helm, Ingress, HPA, réplicas |
| Serverless / AWS | Lambda + SQS + DynamoDB via Terraform |

## Estrutura

```
services/        microsserviços (um diretório por serviço)
infra/           docker-compose, Kubernetes (Helm/Argo CD) e Terraform
docs/adr/        decisões de arquitetura (Architecture Decision Records)
docs/learning/   anotações de estudo de cada conceito
```

## Roteiro

- [ ] **1. Base**: serviços em Arquitetura Hexagonal, Testcontainers, docker-compose
- [ ] **2. Mensageria e consistência**: Kafka, Outbox, idempotência, retry/DLQ, Saga
- [ ] **3. Session e OAuth 2.0/OIDC**: Keycloak, BFF, Redis, resource servers, Client Credentials, Token Exchange, logout
- [ ] **4. Kubernetes**: kind, Helm, Argo CD, OpenTelemetry, HPA
- [ ] **5. Serverless e AWS**: `notifications` em Go na Lambda, SQS, Terraform (LocalStack → AWS)

## Como rodar

_Será preenchido à medida que os serviços forem implementados._
