# Exercícios — Aula 07

!!! note "Lista original da aula"
    O texto abaixo é a lista de exercícios do professor, copiada do arquivo [`aula-07/exercicios.md`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/blob/main/aula-07/exercicios.md) do repositório. Só os níveis de título foram ajustados para o menu do site. Para um caminho sugerido de resolução, veja o [roteiro do estúdio](material.md).

### Objetivo

Estes exercícios dão continuidade aos domínios de Orientação a Objetos anteriores (Campeonato de Futebol e Loja Virtual), desafiando os alunos a transformar classes Java puras em **APIs RESTful** completas utilizando o framework **Spring** e boas práticas de arquitetura em camadas.

Para cada exercício:
1. Utilize o Spring Initializr para estruturar o projeto com as dependências necessárias (`Spring Web`).
2. Separe a aplicação em camadas claras: `Controller`, `Service` e `Model`.

## Exercício 1 – API REST para o Campeonato de Futebol

Implemente uma API REST para gerenciar o campeonato de futebol, aplicando as regras de negócio de pontuação, cadastro de jogadores e registro de partidas.

### Endpoints e Requisitos

#### 1. Gerenciamento de Jogadores (`/jogadores`)
- **`POST /jogadores`**: Cadastra um novo jogador. 
- **`GET /jogadores`**: Lista todos os jogadores cadastrados.
- **`GET /jogadores/{id}`**: Busca um jogador específico por ID.

#### 2. Gerenciamento de Times (`/times`)
- **`POST /times`**: Cadastra um novo time.
- **`GET /times`**: Lista todos os times cadastrados.
- **`GET /times/{id}`**: Busca um time específico por ID, retornando também a lista de seus jogadores.
- **`POST /times/{idTime}/jogadores/{idJogador}`**: Adiciona um novo jogador a um time existente.

#### 3. Gerenciamento de Partidas (`/partidas`)
- **`POST /partidas`**: Registra uma nova partida informando a data, o time mandante e o time visitante. Valide que o mandante e o visitante não podem ser o mesmo time.
- **`PATCH /partidas/{id}/placar`**: Atualiza os gols da partida (`golMandante` e `golVisitante`).
- **`POST /partidas/{id}/cartoes`**: Registra um cartão (`AMARELO` ou `VERMELHO`) para um jogador durante a partida, informando o minuto.

#### 4. Gerenciamento do Campeonato (`/campeonatos`)
- **`GET /campeonatos/{id}/classificacao`**: Retorna a tabela de classificação do campeonato ordenada decrescentemente por pontos. 
  - *Regra de pontuação:* Vitória = 3 pontos, Empate = 1 ponto, Derrota = 0 pontos.
- **`POST /campeonatos`**: Cria um novo campeonato.

## Exercício 2 – API REST para a Loja Virtual (E-commerce)

Desenvolva uma API REST robusta para gerenciar a Loja Virtual, garantindo controle de categorias, histórico de preços em pedidos e paginação.

### Endpoints e Requisitos

#### 1. Categorias e Produtos (`/categorias` e `/produtos`)
- **`POST /categorias`**: Cadastra uma nova categoria.
- **`POST /produtos`**: Cadastra um produto associado a uma categoria existente.
- **`GET /produtos`**: Lista os produtos, permitindo um filtro opcional por categoria através de Query Parameter (`?categoriaId=...`).

#### 2. Clientes (`/clientes`)
- **`POST /clientes`**: Cadastra um novo cliente (valide formato de CPF e unicidade).
- **`GET /clientes`**: Lista todos os cleitnes cadastrados.
- **`GET /clientes/{cpf}/pedidos`**: Lista todos os pedidos realizados por um cliente específico, calculando o valor total gasto em cada um.

#### 3. Pedidos e Itens (`/pedidos`)
- **`POST /pedidos`**: Cria um novo pedido vinculado a um cliente, com status inicial `"ABERTO"`.
- **`POST /pedidos/{id}/itens`**: Adiciona um produto e uma quantidade ao pedido.
  - *Regra crítica:* O sistema deve capturar o preço unitário atual do produto no momento da adição e armazená-lo no `ItemPedido`. Alterações futuras no preço do produto **não** devem afetar pedidos já criados.
  - *Validação:* Impedir a adição de itens caso o status do pedido não seja `"ABERTO"`.
- **`PATCH /pedidos/{id}/status`**: Altera o status do pedido (ex: de `"ABERTO"` para `"PAGO"` ou `"CANCELADO"`).

### Orientações de Entrega

1. Suba o código estruturado em um repositório no GitHub ou via um ZIP no BlackBoard.
2. Inclua um arquivo `README.md` instruindo como rodar a aplicação e testar os endpoints (ex: via cURL, Postman ou arquivo `.http` do IntelliJ).
