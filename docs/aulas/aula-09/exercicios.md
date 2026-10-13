# Exercícios — Aula 09

!!! note "Exercícios do professor, com orientações complementares"
    Os três enunciados abaixo são do professor e estão no fim do [`README.md` da aula](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/blob/main/aula-09/biblioteca/README.md). O objetivo, a forma de conferir e as dicas de cada um são propostas deste site, ainda não revisadas por ele.

Faça depois do [roteiro da aula](material.md), usando a API que você construiu. A numeração é a do README. Se quiser ir do mais simples ao mais trabalhoso, faça na ordem 3, 2, 1.

## Exercício 1 — Recuperação de senha

> Implementar a rota de recuperação de senha

**Objetivo.** Criar uma rota que altera um usuário já cadastrado.

**Uma interpretação possível.** O enunciado não detalha a rota. Uma forma simples de atendê-lo: uma rota aberta, `POST /usuarios/recuperar-senha`, que recebe um e-mail e uma senha nova e troca a senha guardada daquele usuário. Em um sistema real, a recuperação envolveria confirmar por e-mail que o pedido veio do dono da conta. Aqui, basta trocar.

**Como conferir.** Com a Ana cadastrada com a senha `123`, peça a troca para `nova`. Depois:

- `curl -i -u ana@insper.edu.br:123 http://localhost:8080/editora` responde `401`;
- `curl -i -u ana@insper.edu.br:nova http://localhost:8080/editora` responde `200`;
- pedir a troca para um e-mail que não existe não cria um usuário novo.

??? tip "Dica"
    São três alterações: uma regra a mais no `SecurityConfig`, um método no `UsuarioService` e uma rota no `UsuarioController`. A senha nova precisa passar pelo `passwordEncoder` antes de ser guardada, como no cadastro.

## Exercício 2 — Listagem de usuários

> Implementar a rota de listagem de usuários

**Objetivo.** Acrescentar uma rota a um controller que já existe.

**Como conferir.** O `SecurityConfig` da aula já libera `GET /usuarios`, então a rota funciona sem `-u`. Com a Ana cadastrada, `curl http://localhost:8080/usuarios` devolve uma lista com um usuário.

Olhe o que a lista mostra e responda: essa rota deveria mesmo ser aberta a todos? O que você mudaria?

??? tip "Dica"
    O service guarda os usuários em um `HashMap`. Para devolver todos, use `new ArrayList<>(usuarios.values())`, como em `EditoraService.listar`.

## Exercício 3 — Liberar o Swagger

> Liberar o swagger no SecurityConfig

**Objetivo.** Ler e alterar as regras de acesso.

**Como conferir.**

- <http://localhost:8080/swagger-ui.html> abre no navegador sem pedir usuário e senha, e a página lista as rotas;
- `curl -i http://localhost:8080/editora` continua respondendo `401`.

??? tip "Dica"
    A página do Swagger usa mais de um endereço: a página em si, os arquivos dela e a descrição da API que ela lê. Libere `"/swagger-ui.html"`, `"/swagger-ui/**"` e `"/v3/api-docs/**"`. O `**` vale por qualquer coisa depois da barra. As regras novas precisam vir antes de `anyRequest()`.
