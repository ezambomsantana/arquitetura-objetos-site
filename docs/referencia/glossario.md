# Glossário

Consulte os principais termos apresentados nas aulas. Cada definição traz uma explicação breve e um link para a aula em que o conceito é introduzido.

| Termo | Definição | Aula |
|---|---|:---:|
| `401` | Código HTTP que indica que a requisição não foi autenticada. | [09](../aulas/aula-09/material.md) |
| Anotação | Marca uma classe, um método ou um atributo para indicar seu papel ou configurar um comportamento. | [05](../aulas/aula-05/material.md) |
| API REST | API que utiliza conceitos e convenções REST para permitir a comunicação entre sistemas, geralmente por HTTP. | [05](../aulas/aula-05/material.md) |
| Atributo | Dado que representa uma característica ou um estado de um objeto. | [02](../aulas/aula-02/material.md) |
| Autenticação | Processo de verificar a identidade de quem faz uma requisição. | [09](../aulas/aula-09/material.md) |
| `@Autowired` | Anotação do Spring usada para solicitar a injeção de uma dependência. | [06](../aulas/aula-06/material.md) |
| `@Bean` | Anotação que indica que o objeto retornado por um método deve ser registrado como bean no Spring. | [09](../aulas/aula-09/material.md) |
| `Bearer` | Esquema usado para enviar um token no cabeçalho HTTP `Authorization`. | [14](../aulas/aula-14/material.md) |
| BCrypt | Algoritmo de hash de senhas que dificulta a recuperação da senha original. | [09](../aulas/aula-09/material.md) |
| Chave estrangeira | Coluna que referencia um registro de outra tabela, geralmente por meio de seu `id`. | [12](../aulas/aula-12/material.md) |
| Chave secreta | Valor usado para assinar ou verificar a assinatura de tokens, conforme o algoritmo empregado. | [14](../aulas/aula-14/material.md) |
| Classe | Estrutura que define os atributos e os métodos dos objetos criados a partir dela. | [02](../aulas/aula-02/material.md) |
| Classe abstrata | Classe que não pode ser instanciada diretamente e pode servir de base para outras classes. | [11](../aulas/aula-11/material.md) |
| Código de status | Número HTTP que indica o resultado de uma requisição, como sucesso ou erro. | [05](../aulas/aula-05/material.md) |
| Configuração | Classe ou conjunto de definições que ajusta o funcionamento da aplicação ou do Spring. | [09](../aulas/aula-09/material.md) |
| Construtor | Bloco especial executado durante a criação de um objeto, usado para inicializá-lo. | [02](../aulas/aula-02/material.md) |
| Controller | Componente responsável por receber requisições e coordenar as respostas da API. | [05](../aulas/aula-05/material.md) |
| Dependência | Objeto ou componente de que outra parte do programa precisa para funcionar. | [06](../aulas/aula-06/material.md) |
| Dependência do Maven | Biblioteca ou componente declarado no projeto para ser gerenciado pelo Maven. | [08](../aulas/aula-08/material.md) |
| DTO | Objeto usado para transportar dados entre partes do sistema, como na entrada ou na saída de uma API. | [08](../aulas/aula-08/material.md) |
| Encapsulamento | Princípio que protege o estado de um objeto e controla como seus dados são acessados ou alterados. | [03](../aulas/aula-03/material.md) |
| Entidade | Classe que representa um registro persistido no banco de dados, geralmente associado a uma tabela. | [12](../aulas/aula-12/material.md) |
| Exceção | Mecanismo usado para representar uma condição excepcional ou um erro durante a execução. | [08](../aulas/aula-08/material.md) |
| Filtro | Componente que pode interceptar e processar requisições antes que elas cheguem aos controllers. | [14](../aulas/aula-14/material.md) |
| `findById(...).orElseThrow(...)` | Busca um registro pelo identificador e lança uma exceção se o resultado estiver vazio. | [13](../aulas/aula-13/material.md) |
| Getter e setter | Métodos usados, respectivamente, para obter e alterar o valor de um atributo. | [03](../aulas/aula-03/material.md) |
| Herança | Mecanismo pelo qual uma classe deriva de outra e herda seus membros acessíveis. | [11](../aulas/aula-11/material.md) |
| HTTP Basic | Esquema de autenticação HTTP que envia credenciais codificadas em Base64; por isso, deve ser usado com HTTPS. | [09](../aulas/aula-09/material.md) |
| `implements` | Palavra-chave que indica que uma classe implementa uma interface. | [09](../aulas/aula-09/material.md) |
| Injeção de dependência | Técnica em que um componente recebe as dependências de que precisa, em vez de criá-las diretamente. | [06](../aulas/aula-06/material.md) |
| Interface | Contrato que define operações que uma classe pode implementar. | [11](../aulas/aula-11/material.md) |
| `@JsonIgnore` | Anotação do Jackson usada para excluir uma propriedade de determinadas operações de serialização ou desserialização JSON. | [13](../aulas/aula-13/material.md) |
| JWT | Formato de token que pode transportar informações em declarações (*claims*) e incluir uma assinatura, conforme sua configuração. | [14](../aulas/aula-14/material.md) |
| Método | Bloco de código que define um comportamento ou uma operação de uma classe. | [02](../aulas/aula-02/material.md) |
| Método abstrato | Método declarado sem implementação, cuja implementação pode ser exigida das classes concretas derivadas. | [11](../aulas/aula-11/material.md) |
| Método derivado | Método de repositório cujo nome permite ao Spring Data JPA interpretar e gerar uma consulta. | [12](../aulas/aula-12/material.md) |
| Muitos para muitos | Relacionamento em que várias instâncias de uma entidade podem estar ligadas a várias instâncias de outra. | [03](../aulas/aula-03/material.md) |
| Objeto | Instância de uma classe, com estado e comportamentos definidos por ela. | [02](../aulas/aula-02/material.md) |
| Pacote | Mecanismo do Java para organizar classes e interfaces em grupos identificados por um nome. | [08](../aulas/aula-08/material.md) |
| Paginação | Técnica que divide um conjunto de resultados em páginas menores. | [12](../aulas/aula-12/material.md) |
| Persistência | Capacidade de manter dados armazenados mesmo depois que o programa é encerrado. | [12](../aulas/aula-12/material.md) |
| Polimorfismo | Princípio que permite tratar objetos de tipos diferentes por uma interface comum, com comportamentos específicos. | [11](../aulas/aula-11/material.md) |
| `private` | Modificador de acesso que restringe o acesso direto ao membro à própria classe em que ele foi declarado. | [03](../aulas/aula-03/material.md) |
| Referência circular | Situação em que objetos se referenciam direta ou indiretamente, formando um ciclo. | [13](../aulas/aula-13/material.md) |
| Repository | Componente que encapsula operações de acesso e persistência de dados, como consultas ao banco. | [12](../aulas/aula-12/material.md) |
| Rota | Combinação de caminho e método HTTP usada para identificar uma operação disponibilizada pela API. | [05](../aulas/aula-05/material.md) |
| `@Service` | Anotação do Spring que identifica uma classe como componente da camada de serviço e permite seu gerenciamento pelo contêiner. | [06](../aulas/aula-06/material.md) |
| Service | Componente que concentra regras de negócio e coordena operações da aplicação. | [05](../aulas/aula-05/material.md) |
| Singleton | Padrão que restringe uma classe a uma única instância, conforme sua implementação. | [06](../aulas/aula-06/material.md) |
| Sobrescrita | Redefinição, em uma subclasse, de um método herdado para fornecer outro comportamento. | [11](../aulas/aula-11/material.md) |
| Tabela de ligação | Tabela usada para representar associações entre registros, especialmente em relacionamentos muitos para muitos. | [13](../aulas/aula-13/material.md) |
| `throw` | Palavra-chave do Java usada para lançar uma exceção. | [08](../aulas/aula-08/material.md) |
| Token | Sequência de dados usada para representar informações ou conceder acesso, conforme o mecanismo de autenticação. | [14](../aulas/aula-14/material.md) |
| `toString()` | Método que retorna uma representação textual de um objeto. | [03](../aulas/aula-03/material.md) |
| Tratador de erros | Componente que captura ou trata erros e exceções e pode convertê-los em respostas adequadas da API. | [08](../aulas/aula-08/material.md) |
| Um para muitos | Relacionamento em que uma instância de uma entidade pode estar associada a várias instâncias de outra. | [03](../aulas/aula-03/material.md) |
| Usuários persistidos | Contas de usuário armazenadas no banco de dados para serem recuperadas entre execuções da aplicação. | [13](../aulas/aula-13/material.md) |
| Validação | Verificação de dados para determinar se atendem às regras e restrições definidas. | [14](../aulas/aula-14/material.md) |