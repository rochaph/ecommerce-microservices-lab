# Diário de estudos

Registro de cada sessão e banco de perguntas de revisão. A rotina das 15h usa este arquivo para escolher
as perguntas de revisão do dia.

## Banco de revisão

Perguntas curtas, respondíveis em 1–3 frases. Prioridade na revisão: ❌ → ⚠️ → nunca perguntada → ✅ mais antiga.

Resultado: ✅ acertou · ⚠️ parcial · ❌ errou · — ainda não perguntada

| # | Pergunta | Origem | Última revisão | Resultado |
|---|---|---|---|---|
| 1 | Por que Maven e não Gradle neste projeto? | 2026-10-14 | — | — |
| 2 | Para que serve o Maven Wrapper? | 2026-10-14 | — | — |
| 3 | Que problema a arquitetura em camadas tradicional causa no domínio? | 2026-10-15 | — | — |
| 4 | O que é um port de entrada? E um de saída? | 2026-10-15 | — | — |
| 5 | Por que o port de saída é declarado no centro, e não no adapter? | 2026-10-15 | — | — |
| 6 | Qual princípio do SOLID sustenta a Arquitetura Hexagonal? | 2026-10-15 | — | — |
| 7 | Quando a Arquitetura Hexagonal *não* vale a pena? | 2026-10-15 | — | — |
| 8 | Módulo único + ArchUnit vs. multi-módulo: quando cada um detecta a violação? | 2026-10-15 | — | — |
| 9 | Por que o multi-módulo não elimina o ArchUnit? | 2026-10-15 | — | — |
| 10 | Por que os casos de uso não levam `@Service` nem `@Transactional` aqui? | 2026-10-15 | — | — |

## Registro

Uma entrada por sessão, da mais recente para a mais antiga.

<!--
### AAAA-MM-DD · Mxx · título da missão

- **Status:** concluída | parcial | versão mínima
- **Aprendi:** 2–3 frases com as próprias palavras
- **Quiz do dia:** acertos / total; perguntas novas entram no banco de revisão
- **Ponto fraco:** o que precisa voltar na revisão
-->

### 2026-10-15 · Conceito de Arquitetura Hexagonal

- **Status:** concluída (antes da rotina diária existir)
- **Aprendi:** Ports & Adapters; inversão de dependência; diferença entre módulo único + ArchUnit e multi-módulo.
  Decisão: multi-módulo em todos os serviços (vira ADR 0004 na M01).
- **Quiz do dia:** não aplicado. Ficaram em aberto 2 perguntas de checagem:
  1. No caso de uso "criar pedido", quais seriam os ports (in e out), e em qual módulo mora cada interface e cada implementação?
  2. Por que o `OrderRepository` é declarado no `application`, e não no módulo de adapters?
- **Ponto fraco:** ainda não avaliado.

### 2026-10-14 · Ferramenta de build

- **Status:** concluída
- **Aprendi:** Maven vs. Gradle e como escrever um ADR com forças, alternativas justas e custos aceitos.
  Resultado: [ADR 0003](../adr/0003-maven-como-ferramenta-de-build.md).
- **Quiz do dia:** não aplicado.
- **Ponto fraco:** a primeira versão do ADR não tinha contexto específico nem custos.
