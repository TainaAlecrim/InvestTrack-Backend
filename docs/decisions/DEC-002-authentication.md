# DEC-002 — Autenticação por cookie e sessão única

- **Status:** Aprovada para V1
- **Data:** 17/11/2026

## Contexto
A aplicação será consumida por uma interface Web separada e precisa permitir uma única sessão ativa por usuário.

## Decisão
Usar ASP.NET Core Identity com autenticação por cookie HttpOnly. Persistir ActiveSessionId no usuário. A cada login, gerar um novo identificador; validar esse identificador nas requisições protegidas. Não implementar JWT ou refresh token na V1.

## Parâmetros
- Inatividade: 30 minutos, com renovação deslizante.
- Limite absoluto: 12 horas.
- Cookie não persistente.
- Novo login invalida sessão anterior.
- HTTPS em produção, Secure, HttpOnly, SameSite=Lax.
- CORS por origem permitida e antiforgery para operações que alteram estado.

## Consequências
- Simples para aplicação Web.
- Frontend envia cookies e token antiforgery.
- Frontend e API devem ser implantados sob o mesmo site registrável na V1.
- Requisições da sessão antiga recebem 401 na próxima chamada; não há push em tempo real.
