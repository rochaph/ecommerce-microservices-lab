# 2. Java/Spring nos serviços principais e Go no serviço serverless

- **Status:** aceita
- **Data:** 2026-10-13

## Contexto

O projeto precisa de uma stack com bom suporte a OAuth 2.0/OIDC, sessão distribuída, mensageria e testes de integração,
e que seja relevante no mercado de trabalho. O serviço `notifications` roda como AWS Lambda, onde tempo de cold start
e consumo de memória pesam.

## Alternativas consideradas

- **Tudo em Java/Spring**: um único ecossistema; cold start na Lambda exige SnapStart ou GraalVM native.
- **Tudo em Go**: ótimo para Lambda; ecossistema menos completo para OAuth client/sessão e menos comum em vagas de microsserviços corporativos.
- **Java/Spring + Go no `notifications`**: cada linguagem onde ela é mais forte.

## Decisão

Java 21 + Spring Boot 3 em `bff`, `users`, `orders`, `payments` e `inventory`. Go em `notifications`.

## Consequências

- Spring Security, Spring Session, Spring Kafka e Testcontainers cobrem os temas centrais com ferramentas de referência.
- Go entrega Lambdas com cold start baixo e binário pequeno.
- Duas toolchains no CI. Os contratos entre serviços (eventos e APIs) precisam ser explícitos, já que não há código compartilhado.
