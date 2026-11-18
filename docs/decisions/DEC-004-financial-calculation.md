# DEC-004 — Custo médio ponderado móvel

- **Status:** Aprovada para V1
- **Data:** 17/11/2026

## Contexto
É necessário calcular custo das posições de cada ativo com regras simples e consistentes.

## Decisão
Utilizar método de custo médio ponderado móvel. Compras recalculam a média ponderada; vendas reduzem quantidade e preservam a média das unidades restantes. Ao zerar posição, quantidade e média tornam-se zero. Não calcular resultado realizado nem taxas na V1.

## Consequências
- Toda alteração retroativa exige reprocessar operações posteriores.
- Venda não pode levar saldo abaixo de zero.
- Resultado do dashboard será não realizado, baseado nas posições em aberto.
