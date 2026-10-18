# 3. Maven como ferramenta de build

- **Status:** aceita
- **Data:** 2026-10-14

## Contexto

Antes de inicializar os serviços Java é preciso escolher a ferramenta de build. As opções maduras no ecossistema
são Maven e Gradle, e as duas são suportadas pelo Spring Boot.

O projeto tem 5 serviços Java (`bff`, `users`, `orders`, `payments`, `inventory`) com builds independentes e sem
código compartilhado. Cada build é pequeno e isolado, o que reduz as vantagens técnicas que o Gradle tem em monorepos
grandes com muitos módulos interdependentes.

Além disso, o projeto tem como objetivo usar as ferramentas mais frequentes no mercado-alvo: backend Java corporativo,
onde o Maven predomina.

## Alternativas consideradas

- **Gradle**: builds mais rápidos (daemon, build incremental, build cache) e fácil de estender, já que o build script
  é código. Nesse projeto essas vantagens pesam pouco: com builds pequenos e isolados, o ganho de performance por build
  é marginal, e a flexibilidade de scripts não é necessária.
- **Maven**: modelo declarativo, convenção acima de configuração e ciclo de vida fixo e previsível
  (`validate → compile → test → package → verify → install`).

## Decisão

Usar Maven, com Maven Wrapper, em todos os serviços Java.

A padronização é o argumento principal: com vários serviços, todo `pom.xml` tem a mesma estrutura e o mesmo ciclo de
vida, então quem entende um serviço entende todos. Soma-se a isso o objetivo de ganhar experiência sólida com a
ferramenta mais usada no mercado-alvo.

## Consequências

- Cada serviço tem o próprio `pom.xml` e o próprio Maven Wrapper (`mvnw` versionado), que fixa a versão do Maven
  e garante builds reproduzíveis sem depender do que está instalado na máquina ou no CI.
- Cada serviço herda de `spring-boot-starter-parent`. As versões de dependências ficam duplicadas entre os serviços,
  custo aceito para manter a independência; um BOM próprio pode resolver isso no futuro.
- CI e desenvolvedores rodam `./mvnw verify`. Os testes de arquitetura (ArchUnit) quebram o build nessa fase.
- Custo aceito em performance: sem build cache nem build incremental equivalentes aos do Gradle, o build pode ser
  mais demorado.
- Custo aceito em customização: o `pom.xml` é declarativo (XML verboso) e não aceita scripts; lógica de build fora do
  padrão exige plugins.
