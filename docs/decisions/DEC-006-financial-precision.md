# DEC-006 — Precisão financeira decimal

- **Status:** Aprovada para V1
- **Data:** 17/11/2026

## Contexto
Cálculos financeiros não devem sofrer erros de representação binária típicos de ponto flutuante.

## Decisão
Utilizar decimal no C# e numeric(18,6) no PostgreSQL para quantidades, preços e valores internos. Arredondar para duas casas somente na apresentação monetária.

## Consequências
- Não utilizar float ou double nos cálculos financeiros.
- Testes devem cobrir precisão e arredondamento.
