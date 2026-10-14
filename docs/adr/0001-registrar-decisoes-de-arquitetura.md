# 1. Registrar decisões de arquitetura

- **Status:** aceita
- **Data:** 2026-10-13

## Contexto

O projeto envolve muitas escolhas com trade-offs (broker, modelo de sessão, estilo de Saga, onde rodar cada serviço).
Sem registro, o motivo de cada escolha se perde, e é justamente esse motivo que importa numa entrevista técnica.

## Decisão

Registrar cada decisão relevante como um ADR (Architecture Decision Record) curto em `docs/adr/`,
numerado em sequência e seguindo o modelo em [`template.md`](template.md).

## Consequências

- Cada decisão fica com contexto, alternativas consideradas e consequências.
- ADRs não são editados depois de aceitos: uma decisão nova substitui a antiga com o status "substituída por N".
