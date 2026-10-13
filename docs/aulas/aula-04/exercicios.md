# Exercícios — Aula 04

!!! note "Lista original da aula"
    O texto abaixo é a lista de exercícios do professor, copiada do arquivo [`aula-04/exercicios.md`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/blob/main/aula-04/exercicios.md) do repositório. Só os níveis de título foram ajustados para o menu do site. Para um caminho sugerido de resolução, veja o [roteiro do estúdio](material.md).

### Objetivo

Os exercícios abaixo têm como objetivo praticar:

- Criação de classes e objetos;
- Atributos e métodos;
- Relacionamentos entre objetos;
- Relacionamentos **1 para 1**;
- Relacionamentos **1 para N**;
- Relacionamentos **N para N**;
- Uso de coleções, como `ArrayList`;
- Navegação entre objetos relacionados.

Para cada exercício:

1. Implemente todas as classes apresentadas.
2. Crie os atributos indicados.
3. Crie construtores.
4. Crie getters e setters quando necessário.
5. Implemente os métodos solicitados.
6. Crie uma classe `Main` para testar o sistema.

## Exercício 1 – Campeonato de Futebol

Implemente um sistema para controlar um campeonato de futebol.

### Classe `Campeonato`

#### Atributos

- `nome`
- `ano`
- `times`
- `partidas`

#### Métodos

- `adicionarTime(Time time)`
- `adicionarPartida(Partida partida)`
- `listarTimes()`
- `listarPartidas()`
- `buscarTime(String nome)`
- `exibirClassificacao()`

O método `exibirClassificacao()` deve mostrar os times ordenados pela quantidade de pontos.

Para calcular a pontuação, considere:

| Resultado | Pontos |
|---|---:|
| Vitória | 3 |
| Empate | 1 |
| Derrota | 0 |

### Classe `Time`

#### Atributos

- `nome`
- `cidade`
- `jogadores`

#### Métodos

- `adicionarJogador(Jogador jogador)`
- `removerJogador(Jogador jogador)`
- `listarJogadores()`
- `mediaIdadeJogadores()`

### Classe `Jogador`

#### Atributos

- `nome`
- `numero`
- `posicao`
- `time`
- `idade`

### Classe `Partida`

#### Atributos

- `data`
- `timeMandante`
- `timeVisitante`
- `golMandante`
- `golVisitate`
- `cartoes`
- `campeonato`

#### Métodos

- `registrarTimes(Time timeMandante, Time timeVisitante)`
- `registrarGol(int golMandante, int golVisitante)`
- `registrarCartao(Cartao cartao)`
- `buscarVencedor()`
- `exibirPlacar()`

### Classe `Cartao`

#### Atributos

- `jogador`
- `minuto`
- `tipo`
- `partida`

O tipo do cartão pode ser `"AMARELO"` ou `"VERMELHO"`.

### Teste

Na classe `Main`:

1. Crie um campeonato.
2. Crie pelo menos 4 times.
3. Crie pelo menos 3 jogadores para cada time.
4. Crie pelo menos 6 partidas.
5. Registre gols e cartões.
6. Exiba os resultados das partidas.
7. Exiba a classificação do campeonato.
8. Mostre o artilheiro do campeonato.

## Exercício 2 – Loja Virtual

Implemente um sistema para representar uma loja virtual.

### Classe `Loja`

#### Atributos

- `nome`
- `produtos`
- `clientes`

#### Métodos

- `adicionarProduto(Produto produto)`
- `adicionarCliente(Cliente cliente)`
- `buscarProduto(int codigo)`
- `buscarCliente(String cpf)`
- `listarProdutos()`
- `listarProdutosPorCategoria(Categoria categoria)`

### Classe `Produto`

#### Atributos

- `codigo`
- `nome`
- `preco`
- `categoria`

#### Métodos

- `aplicarDesconto(double percentual)`
- `aumentarPreco(double percentual)`

### Classe `Categoria`

#### Atributos

- `codigo`
- `nome`
- `produtos`

#### Métodos

- `adicionarProduto(Produto produto)`
- `listarProdutos()`
- `calcularPrecoMedio()`
- `buscarProdutoMaisCaro()`

### Classe `Cliente`

#### Atributos

- `nome`
- `cpf`
- `email`
- `pedidos`

#### Métodos

- `adicionarPedido(Pedido pedido)`
- `listarPedidos()`
- `calcularTotalGasto()`
- `buscarPedidoMaisCaro()`

### Classe `Pedido`

#### Atributos

- `numero`
- `cliente`
- `itens`
- `status`

#### Métodos

- `adicionarProduto(Produto produto, int quantidade)`
- `removerProduto(Produto produto)`
- `calcularTotal()`
- `calcularQuantidadeItens()`
- `buscarItemMaisCaro()`
- `alterarStatus(String status)`

### Classe `ItemPedido`

#### Atributos

- `produto`
- `quantidade`
- `precoUnitario`

#### Métodos

- `calcularSubtotal()`

O `precoUnitario` deve armazenar o preço do produto no momento em que ele foi adicionado ao pedido.

Dessa forma, alterar posteriormente o preço do produto não deve modificar pedidos antigos.

### Teste

Na classe `Main`:

1. Crie pelo menos 3 categorias.
2. Crie pelo menos 8 produtos.
3. Crie pelo menos 3 clientes.
4. Crie diferentes pedidos.
5. Adicione produtos e quantidades aos pedidos.
6. Exiba o valor de cada pedido.
7. Mostre quanto cada cliente já gastou.
8. Mostre o produto mais caro de cada categoria.
