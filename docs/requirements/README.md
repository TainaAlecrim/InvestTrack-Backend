# Requisitos

Esta pasta reúne a baseline funcional do InvestTrack Backend. Os documentos abaixo foram escritos para que a implementação possa seguir critérios explícitos, sem decisões de produto durante a codificação.

## Documentos

| Documento | Conteúdo | Status |
|---|---|---|
| [01-product-vision.md](01-product-vision.md) | Problema, objetivo, público e visão | Definido |
| [02-project-scope.md](02-project-scope.md) | Escopo e limites da versão 1.0 | Definido |
| [03-functional-requirements.md](03-functional-requirements.md) | Requisitos funcionais por módulo | Definido |
| [04-non-functional-requirements.md](04-non-functional-requirements.md) | Segurança, desempenho, qualidade e operação | Definido |
| [05-business-rules.md](05-business-rules.md) | Regras de negócio e cálculos | Definido |
| [06-use-cases.md](06-use-cases.md) | Casos de uso e fluxos principais | Definido |
| [07-acceptance-criteria.md](07-acceptance-criteria.md) | Critérios verificáveis de aceite | Definido |

## Módulos funcionais

1. Autenticação e usuários
2. Categorias fixas e ativos
3. Movimentações
4. Posições e cálculos financeiros
5. Atualização manual de preços
6. Dashboard
7. Histórico patrimonial diário

## Convenção de mudança

Se durante a implementação surgir uma dúvida não coberta:
1. Não inventar regra silenciosamente.
2. Conferir os documentos de requisitos e decisões.
3. Registrar a dúvida como issue ou nota de decisão.
4. Alterar a especificação antes de implementar comportamento diferente.

Os requisitos desta pasta são a referência principal para o desenvolvimento da V1.
