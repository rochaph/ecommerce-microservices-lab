# Plano diário

Uma missão por dia, de ~60 minutos, com começo, meio e fim. A rotina das 15h lê este arquivo e o
[diário](diario.md) para propor a missão do dia e as perguntas de revisão.

## Regras

- **Uma missão por dia.** Terminou cedo? Pare ou revise. Não puxe a próxima.
- **Pulou um dia? Sem acúmulo.** No dia seguinte a missão é a mesma, nunca duas.
- **Dia difícil? Faça a versão mínima.** Ela mantém o hábito vivo, e isso vale mais que a missão inteira.
- **Toda sessão segue o ciclo:** revisão (perguntas curtas de dias anteriores) → missão → quiz do dia → registro no diário → commit e push.
- O plano é vivo: missões podem ser quebradas, juntadas ou reordenadas na revisão de cada bloco.

## Progresso

| Missão | Bloco | Status |
|---|---|---|
| M01 · Fechar a fundação do `orders` | 1 | pendente |
| M02 · Esqueleto multi-módulo do `orders` | 1 | pendente |
| M03 · Agregado `Order` | 1 | pendente |
| M04 · Caso de uso "criar pedido" | 1 | pendente |
| M05 · Consultar e cancelar pedido | 1 | pendente |
| M06 · Testes de arquitetura com ArchUnit | 1 | pendente |
| M07 · Adapter REST de entrada | 1 | pendente |
| M08 · Fronteira transacional | 1 | pendente |
| M09 · Adapter JPA de saída | 1 | pendente |
| M10 · Testcontainers no adapter JPA | 1 | pendente |
| M11 · docker-compose ponta a ponta | 1 | pendente |
| M12 · Fechamento do Bloco 1 | 1 | pendente |

Status possíveis: `pendente`, `em andamento`, `concluída`.

## Bloco 1: Arquitetura Hexagonal no `orders`

O `orders` é o serviço de referência. Os demais nascem nos blocos em que entram em cena:
`payments` e `inventory` no Bloco 2, `users` e `bff` no Bloco 3.

### M01 · Fechar a fundação do `orders`

- **Por quê:** fechar as decisões antes de gerar código evita retrabalho na estrutura.
- **Primeiro passo (5 min):** responder as 2 perguntas de checagem sobre ports (sessão de 2026-10-15 no diário).
- **Pronto quando:**
  - [ ] Perguntas de checagem respondidas e revisadas
  - [ ] ADR 0004 (multi-módulo em todos os serviços) escrito e aceito
  - [ ] ADRs 0003 e 0004 commitados
- **Conceito de entrevista:** como garantir que as dependências apontem para dentro (compilador vs. teste).
- **Versão mínima:** responder as 2 perguntas de checagem.

### M02 · Esqueleto multi-módulo do `orders`

- **Por quê:** a estrutura de módulos é o que faz o compilador garantir as fronteiras.
- **Primeiro passo (5 min):** desenhar no papel os 4 módulos e as setas de dependência entre eles.
- **Pronto quando:**
  - [ ] `services/orders` com POM parent/agregador e módulos `orders-domain`, `orders-application`, `orders-adapters`, `orders-bootstrap`
  - [ ] Maven Wrapper versionado
  - [ ] `orders-domain` sem nenhuma dependência de framework
  - [ ] `./mvnw verify` verde e a aplicação Spring Boot sobe vazia
- **Conceito de entrevista:** reactor do Maven, parent vs. agregador, `dependencyManagement` vs. `dependencies`.
- **Versão mínima:** POM parent e `orders-domain` compilando.

### M03 · Agregado `Order`

- **Por quê:** o domínio é o centro do hexágono; tudo depois depende dele estar certo.
- **Primeiro passo (5 min):** listar 3 regras de negócio de um pedido (ex.: não confirmar pedido sem itens).
- **Pronto quando:**
  - [ ] `Order`, `OrderItem`, `OrderStatus` e um value object `Money`
  - [ ] Invariantes protegidas no próprio agregado (sem setters públicos)
  - [ ] Testes unitários puros cobrindo as regras
- **Conceito de entrevista:** agregado, value object, invariantes, modelo rico vs. anêmico.
- **Versão mínima:** `Money` com testes.

### M04 · Caso de uso "criar pedido"

- **Por quê:** é aqui que ports de entrada e de saída deixam de ser teoria.
- **Primeiro passo (5 min):** escrever só as assinaturas das interfaces dos ports.
- **Pronto quando:**
  - [ ] Port in `PlaceOrderUseCase` com um comando de entrada
  - [ ] Port out `OrderRepository` declarado no `application`
  - [ ] Implementação do caso de uso
  - [ ] Teste unitário com repositório em memória
- **Conceito de entrevista:** ports in/out, inversão de dependência, comandos.
- **Versão mínima:** ports declarados e compilando.

### M05 · Consultar e cancelar pedido

- **Por quê:** transições de estado são onde regras de domínio costumam vazar para fora do agregado.
- **Primeiro passo (5 min):** desenhar a máquina de estados do pedido.
- **Pronto quando:**
  - [ ] Caso de uso de consulta (port de query separado do de comando)
  - [ ] Caso de uso de cancelamento com transição validada no agregado
  - [ ] Testes unitários das transições inválidas
- **Conceito de entrevista:** máquina de estados no domínio, separação comando/consulta.
- **Versão mínima:** máquina de estados desenhada e testada no agregado.

### M06 · Testes de arquitetura com ArchUnit

- **Por quê:** o multi-módulo não protege as regras *dentro* de cada módulo.
- **Primeiro passo (5 min):** listar 3 regras que o compilador não consegue garantir.
- **Pronto quando:**
  - [ ] Regras ArchUnit rodando no `verify` (ex.: adapters não se chamam entre si)
  - [ ] Uma violação proposital quebra o build (e depois é revertida)
- **Conceito de entrevista:** fitness functions de arquitetura.
- **Versão mínima:** uma regra funcionando.

### M07 · Adapter REST de entrada

- **Por quê:** é o primeiro adapter; mostra o wiring sem anotações Spring no centro.
- **Primeiro passo (5 min):** escrever o JSON de request e response de "criar pedido".
- **Pronto quando:**
  - [ ] Controller e DTOs no `orders-adapters`, com mapeamento para o comando
  - [ ] Casos de uso registrados via `@Configuration` + `@Bean` no `orders-bootstrap`
  - [ ] Erros de domínio respondidos como Problem Details (RFC 9457)
  - [ ] Teste do controller
- **Conceito de entrevista:** por que DTO ≠ objeto de domínio; wiring explícito.
- **Versão mínima:** endpoint de criação respondendo.

### M08 · Fronteira transacional

- **Por quê:** o caso de uso não conhece Spring, mas precisa de transação.
- **Primeiro passo (5 min):** listar as opções (adapter de entrada, decorator com `TransactionTemplate`, outra).
- **Pronto quando:**
  - [ ] Decisão registrada em ADR
  - [ ] Implementada e testada
- **Conceito de entrevista:** transação em arquitetura hexagonal.
- **Versão mínima:** ADR escrito.

### M09 · Adapter JPA de saída

- **Por quê:** separa o modelo de persistência do modelo de domínio.
- **Primeiro passo (5 min):** escrever a migration Flyway da tabela de pedidos.
- **Pronto quando:**
  - [ ] Entidades JPA separadas do domínio, com mapper
  - [ ] Adapter implementando `OrderRepository`
  - [ ] Migrations Flyway
- **Conceito de entrevista:** modelo de persistência vs. domínio; versionamento de schema.
- **Versão mínima:** migration e entidade JPA.

### M10 · Testcontainers no adapter JPA

- **Por quê:** testar o adapter contra um Postgres real, não contra H2.
- **Primeiro passo (5 min):** escrever o nome dos cenários de teste.
- **Pronto quando:**
  - [ ] Teste de integração do adapter com Postgres via Testcontainers
  - [ ] Roda no `./mvnw verify`
- **Conceito de entrevista:** por que não H2; `@ServiceConnection`; custo de testes de integração.
- **Versão mínima:** container subindo num teste.

### M11 · docker-compose ponta a ponta

- **Por quê:** ver o serviço rodando de verdade fecha o ciclo.
- **Primeiro passo (5 min):** listar os serviços do compose (Postgres e `orders`).
- **Pronto quando:**
  - [ ] `infra/docker-compose` com Postgres e `orders`, configuração via `.env` (só `.env.example` versionado)
  - [ ] Criar e consultar um pedido com `curl`
  - [ ] Seção "Como rodar" do README preenchida
- **Conceito de entrevista:** configuração externa (12-factor), healthchecks.
- **Versão mínima:** Postgres no compose e o serviço conectando.

### M12 · Fechamento do Bloco 1

- **Por quê:** consolidar o bloco é o que transforma prática em resposta de entrevista.
- **Primeiro passo (5 min):** reler os ADRs do bloco.
- **Pronto quando:**
  - [ ] `docs/learning/arquitetura-hexagonal.md` no formato do README da pasta
  - [ ] Simulado de entrevista do bloco (10 perguntas)
  - [ ] Item 1 do roteiro marcado no README
  - [ ] Missões do Bloco 2 detalhadas neste arquivo
- **Versão mínima:** anotação de estudo escrita.

## Próximos blocos

Detalhados em missões ao fim do bloco anterior.

- **Bloco 2, Mensageria e consistência:** Kafka, Transactional Outbox, consumidor idempotente, retry/DLQ, Saga
  (`payments` e `inventory`)
- **Bloco 3, Session e OAuth 2.0/OIDC:** Keycloak, BFF com sessão no Redis, Authorization Code + PKCE, resource
  servers, Client Credentials, Token Exchange, logout (`users` e `bff`)
- **Bloco 4, Kubernetes:** kind, Helm, Argo CD, OpenTelemetry, HPA
- **Bloco 5, Serverless e AWS:** `notifications` em Go na Lambda, SQS, Terraform (LocalStack → AWS)
