# CLAUDE.md

E-commerce simplificado para praticar arquitetura distribuída. Arquitetura, stack e roteiro estão no [README](README.md);
decisões de arquitetura em [docs/adr](docs/adr).

## Estrutura

- `services/<nome>/`: um diretório por microsserviço (`bff`, `users`, `orders`, `payments`, `inventory` em Java; `notifications` em Go)
- `infra/`: docker-compose, Kubernetes (Helm/Argo CD) e Terraform
- `docs/adr/`: Architecture Decision Records, numerados em sequência a partir de `template.md`
- `docs/learning/`: anotações de estudo por conceito

## Regras de arquitetura

- **Arquitetura Hexagonal em todos os serviços**: `domain` → `application` (casos de uso + portas `in`/`out`) → `adapters` (`in`/`out`).
  - As dependências apontam para dentro. `domain` não depende de framework (sem anotações Spring, JPA ou Jackson) nem de infraestrutura.
  - Frameworks e tecnologias aparecem apenas em `adapters` e na configuração.
  - As fronteiras são verificadas por testes de arquitetura (ArchUnit), que devem quebrar o build quando violadas.
- **Cada serviço é dono dos seus dados**: banco próprio; nenhum serviço acessa o banco de outro.
- **Nada de código de domínio compartilhado entre serviços.** A integração acontece por contratos explícitos: APIs REST e eventos versionados.
- **Serviços stateless**: estado de sessão fica no Redis (via BFF), nunca na memória de uma instância.
- Eventos publicados via Transactional Outbox; consumidores precisam ser idempotentes.

## Convenções

- Java 21 + Spring Boot 4 nos serviços Java; Go no `notifications`.
- Commits no padrão Conventional Commits (`feat:`, `fix:`, `chore:`, `docs:`, `test:`, `refactor:`).
- Decisão com trade-off relevante vira ADR antes ou junto com a implementação.
- Documentação em português do Brasil.

## Testes

- `domain` e `application`: testes unitários puros, sem subir Spring, usando adaptadores em memória.
- Adaptadores: testes de integração com Testcontainers (Postgres, Kafka, Redis, Keycloak reais).

## Segredos e custos

- Nunca versionar segredos. Configuração sensível vai em `.env` (ignorado); versionar apenas `.env.example`.
- Tudo roda localmente por padrão (docker-compose, kind, LocalStack).
- Recursos na AWS real só via Terraform, no plano gratuito, e destruídos (`terraform destroy`) ao fim de cada laboratório.
  Não deixar ligados recursos cobrados por hora: EKS, NAT Gateway, Load Balancers, RDS, IPv4 público.

## Como rodar

_Será preenchido à medida que os serviços forem implementados._
