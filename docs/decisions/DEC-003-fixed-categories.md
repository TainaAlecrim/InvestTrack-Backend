# DEC-003 — Categorias fixas de investimento

- **Status:** Aprovada para V1
- **Data:** 17/11/2026

## Contexto
O produto não terá carteiras personalizadas na primeira versão.

## Decisão
Disponibilizar exatamente três categorias controladas pelo backend: Ações, Fundos Imobiliários e Renda Fixa. Representá-las por enum/valor controlado, sem CRUD de categorias. Cada ativo pertence a uma categoria e não muda de categoria após receber movimentações.

## Consequências
- Não há tela ou endpoint de gerenciamento de carteiras.
- Ativos permanecem em uma única categoria.
- Mesmo código pode existir em contas diferentes.
