# Aula 07 — Aula estúdio: exercícios de APIs REST

!!! abstract "Nesta aula"
    - **Formato:** aula estúdio. Você resolve os exercícios do professor, que estão na página [Exercícios](exercicios.md).
    - **Você pratica:** transformar classes em uma API REST, com controllers e services.
    - **Você usa como base:** a API das aulas [05](../aula-05/material.md) e [06](../aula-06/material.md).
    - **Resolução:** não há resolução no repositório.

## Como funciona esta aula

Esta é outra aula estúdio. Você volta aos dois sistemas da [Aula 04](../aula-04/material.md), o **campeonato de futebol** e a **loja virtual**, e transforma cada um em uma API REST, do jeito que a biblioteca foi transformada nas aulas [05](../aula-05/material.md) e [06](../aula-06/material.md).

As classes de dados você já tem, da Aula 04. O trabalho novo é criar os services e os controllers.

## Antes de começar

Crie um projeto para cada exercício em [start.spring.io](https://start.spring.io), com as mesmas opções do Passo 1 da Aula 05, e copie para ele as classes que você escreveu na Aula 04.

O enunciado pede três camadas. Você já conhece as três:

| Camada | Papel | Na biblioteca |
| --- | --- | --- |
| Model | As classes de dados | `Livro`, `Editora` |
| Service | Guarda os objetos e aplica as regras | `LivroService`, `EditoraService` |
| Controller | Recebe a requisição e chama o service | `LivroController`, `EditoraController` |

E quase tudo o que as rotas pedem já apareceu:

| Se a rota pede | Você já fez igual em |
| --- | --- |
| `POST` que cadastra e devolve o objeto | `POST /editora` |
| `GET` que lista tudo | `GET /editora` |
| `GET` com `{id}` no endereço | `GET /editora/{id}` |
| Filtro opcional depois do `?` | `GET /livro?nome=...` |
| Cadastrar algo ligado a outro objeto que já existe | `LivroService.criar`, na Aula 06 |

## Um caminho para resolver

**1. Faça uma entidade por vez, do começo ao fim.** Escolha a mais simples, como `Jogador` ou `Categoria`. Escreva o service, o controller e teste as rotas com `curl` antes de passar para a próxima. Não escreva todos os controllers para testar só no final.

**2. Comece por quem não depende de ninguém.** Um time precisa de jogadores, uma partida precisa de times, um produto precisa de uma categoria. Uma ordem possível:

| Exercício | Ordem sugerida |
| --- | --- |
| Campeonato de Futebol | jogadores → times → partidas → campeonato |
| Loja Virtual | categorias → produtos → clientes → pedidos |

**3. Quando um service precisar de outro, use `@Autowired`.** Para adicionar um jogador a um time, o service de times precisa encontrar o jogador. É a mesma situação do `LivroService`, que precisava do `EditoraService`.

**4. Deixe as regras para depois que o cadastro funcionar.** A pontuação, o status do pedido e o preço guardado no item são regras do service. Faça primeiro a rota funcionar sem a regra, e então acrescente.

## Pontos que costumam travar

??? tip "O verbo PATCH"
    Duas rotas usam `PATCH`, que ainda não apareceu nas aulas. Ele serve para alterar parte de um objeto que já existe. No controller, a anotação é `@PatchMapping`, usada do mesmo jeito que `@PostMapping`.

??? tip "Rotas com dois valores no endereço"
    Em `POST /times/{idTime}/jogadores/{idJogador}`, o método do controller recebe dois parâmetros, cada um com o seu `@PathVariable`.

??? tip "Validações"
    O enunciado pede validações, como impedir que o mandante e o visitante sejam o mesmo time. As aulas ainda não mostraram como devolver um erro ao cliente. Por enquanto, recuse a operação como o `LivroService.criar` da Aula 06 faz quando não encontra a editora: sem salvar e devolvendo `null`. Devolver uma mensagem de erro com o código certo é assunto da aula seguinte.

??? tip "A classificação do campeonato"
    A rota devolve uma lista com o nome e os pontos de cada time, mas nenhuma classe do enunciado tem esses dois dados juntos. Você pode criar uma classe pequena só para essa resposta, com os atributos `nome` e `pontos` e seus getters. O Spring a transforma em JSON como qualquer outra.

??? tip "Uma resposta que nunca termina"
    Se a resposta de uma rota vier enorme ou der erro, veja se dois objetos apontam um para o outro e ambos têm getter, como `Time.getJogadores()` e `Jogador.getTime()`. O Spring monta o JSON seguindo os getters: do time vai para os jogadores, de cada jogador volta para o time, e não para mais. Na biblioteca isso não acontecia porque `Editora` não tem um getter para a lista de livros. Tire o getter de um dos lados.

## Depois de terminar

O repositório não tem resolução desta lista. Para conferir a sua:

- [ ] cada rota do enunciado responde, com o verbo e o endereço pedidos;
- [ ] os cadastros respondem com `201`;
- [ ] os dados cadastrados por uma rota aparecem nas outras, o que mostra que os services são compartilhados;
- [ ] as regras do enunciado funcionam: a pontuação, o preço guardado no item e o bloqueio de itens em pedido que não está aberto;
- [ ] o projeto tem um `README.md` explicando como rodar e testar, como o enunciado pede.
