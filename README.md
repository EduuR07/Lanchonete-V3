# 🍔 Lanchonete V3 — Persistência de Dados com Room

Aplicativo Android de lanchonete desenvolvido em **Kotlin** e **Jetpack Compose**, com gerenciamento de produtos, carrinho de compras e fluxo de pedidos.

Esta terceira versão dá continuidade aos Trabalhos 1 e 2, implementando **persistência local com Room (SQLite)** e operações de CRUD (*Create, Read, Update e Delete*).

### Informações acadêmicas

**Instituição:** Universidade Positivo  
**Curso:** Análise e Desenvolvimento de Sistemas (ADS)  
**Disciplina:** Desenvolvimento Mobile  
**Trabalho:** Trabalho 3 — Persistência de Dados com Room

**Integrantes:**
- Eduardo Rodrigues Bueno
- Eduardo Serviam Betio Gomes
- Matheus Machado Komninou

---

## 1. Sobre o projeto

A Lanchonete é um aplicativo Android que simula o funcionamento de um sistema de pedidos, permitindo visualizar produtos, consultar detalhes, adicionar itens ao carrinho e realizar um fluxo demonstrativo de finalização de compra.

Nos trabalhos anteriores, desenvolvemos a interface e a navegação entre as telas. Entretanto, os produtos eram definidos em uma lista fixa no código, enquanto os itens do carrinho eram armazenados apenas na memória.

Na V3, nosso objetivo foi resolver essa limitação utilizando o **Room**, biblioteca de persistência do Android que trabalha sobre o SQLite.

Com essa implementação, os produtos e itens do carrinho passam a ser armazenados localmente, permitindo que os dados permaneçam disponíveis mesmo após fechar e reabrir o aplicativo.

## 2. Evolução do Trabalho 2 para o Trabalho 3

| Trabalho 2 | Trabalho 3 |
|---|---|
| Produtos definidos em lista fixa | Produtos consultados no Room |
| Carrinho armazenado em memória | Carrinho persistido no SQLite |
| Detalhes recebiam o objeto do produto | Detalhes consultam o produto pelo ID |
| Sem gerenciamento de produtos | Cadastro, edição e exclusão |
| Dados sem persistência | Dados armazenados localmente |

Preservamos as principais funcionalidades desenvolvidas anteriormente, como a tela inicial, cardápio, detalhes, carrinho e checkout.

Também adicionamos uma nova aba chamada **Gerenciar**, responsável por permitir o cadastro e a manutenção dos produtos.

Os oito produtos originais são utilizados para popular o banco na primeira inicialização. Depois disso, o Room passa a ser a fonte dos dados exibidos pelo aplicativo.

## 3. Funcionalidades

- **Tela inicial:** visualização das categorias e produtos em destaque.
- **Cardápio:** listagem dos produtos, filtros por categoria e pesquisa.
- **Detalhes:** consulta dos dados de um produto diretamente no banco pelo ID.
- **Carrinho:** adição e remoção de quantidades, subtotal e taxa de entrega.
- **Gerenciamento:** cadastro, consulta, edição e exclusão de produtos.
- **Checkout:** preenchimento do endereço, seleção do pagamento e confirmação demonstrativa do pedido.
- **Persistência:** armazenamento local dos produtos e dos itens do carrinho.

## 4. Tecnologias utilizadas

- **Kotlin:** linguagem de programação utilizada no desenvolvimento.
- **Jetpack Compose:** construção das interfaces.
- **Material 3:** componentes visuais e estilização.
- **Room:** gerenciamento da persistência de dados.
- **SQLite:** banco de dados local.
- **KSP:** processamento das anotações do Room.
- **Kotlin Coroutines:** execução de operações assíncronas.
- **Flow:** observação das alterações nos dados.
- **Android Studio e Gradle:** ambiente de desenvolvimento e gerenciamento do projeto.

## 5. Estrutura do banco de dados

Para atender aos requisitos do trabalho, criamos duas entidades, dois DAOs e uma classe responsável pelo banco de dados.

### 5.1. ProdutoEntity

A entidade `ProdutoEntity` representa a tabela `produtos`.

Seus campos são:

- `id`: identificador único, com geração automática.
- `nome`: nome do produto.
- `descricao`: informações apresentadas ao usuário.
- `preco`: valor do produto.
- `categoria`: classificação do item no cardápio.

Escolhemos esses campos porque já eram utilizados no Trabalho 2 e são necessários para exibir, cadastrar e modificar os produtos.

O campo `id` utiliza `@PrimaryKey(autoGenerate = true)`.

### 5.2. ItemCarrinhoEntity

A entidade `ItemCarrinhoEntity` representa a tabela `itens_carrinho`.

Ela possui:

- `produtoId`: identificador do produto.
- `quantidade`: quantidade adicionada ao carrinho.

Decidimos armazenar apenas o ID e a quantidade, em vez de duplicar todas as informações do produto.

O campo `produtoId` possui uma relação com `ProdutoEntity`, utilizando uma chave estrangeira com `onDelete = CASCADE`.

Assim, se um produto for excluído do banco, seu item correspondente também será removido do carrinho.

## 6. DAOs e operações CRUD

Criamos dois DAOs para organizar o acesso ao banco.

### ProdutoDao

Responsável pelo gerenciamento dos produtos.

Contém os métodos:

- `inserir()`: cadastra um produto.
- `buscarTodos()`: consulta os produtos cadastrados.
- `buscarPorId()`: consulta um produto específico.
- `atualizar()`: altera um registro existente.
- `deletar()`: exclui um produto.

Também implementamos métodos de observação com `Flow`.

### ItemCarrinhoDao

Responsável pelo gerenciamento dos itens do carrinho.

Possui operações de inserção, consulta, atualização e exclusão, além de uma função para limpar o carrinho.

Utilizamos `@Insert`, `@Query`, `@Update` e `@Delete` para definir as operações.

Os métodos de observação com `Flow` permitem atualizar automaticamente a interface quando os dados são modificados.

## 7. AppDatabase e arquitetura

A classe `AppDatabase` herda de `RoomDatabase` e registra as entidades e seus respectivos DAOs.

Implementamos o padrão **Singleton** para manter uma única instância do banco durante a execução do aplicativo.

Também criamos `RepositorioLanchonete`, responsável por centralizar as consultas e operações de persistência.

As operações de banco são executadas utilizando corrotinas e `Dispatchers.IO`, evitando bloquear a interface principal.

Para operações que exigem consistência, como a atualização das quantidades do carrinho, utilizamos transações com `withTransaction`.

A comunicação segue esta organização:

**Interface Jetpack Compose → RepositorioLanchonete → DAOs → Room → SQLite**

Essa separação ajuda a organizar o código e evita que as telas precisem executar diretamente as operações do banco.

## 8. Processo de desenvolvimento

A implementação foi dividida em etapas.

### Etapa 1 — Análise do projeto anterior

Identificamos as funcionalidades que já existiam e os pontos que precisavam de persistência, principalmente a lista de produtos e o carrinho.

### Etapa 2 — Configuração do Room

Adicionamos as dependências do Room, KSP e corrotinas aos arquivos Gradle do projeto.

### Etapa 3 — Modelagem das entidades

Criamos `ProdutoEntity` e `ItemCarrinhoEntity`, escolhendo os campos necessários para o funcionamento do aplicativo.

### Etapa 4 — Implementação dos DAOs

Criamos os métodos de inserção, consulta, atualização e exclusão, incluindo consultas por ID e observação por `Flow`.

### Etapa 5 — Banco de dados e repositório

Implementamos `AppDatabase` com Singleton e `RepositorioLanchonete` para centralizar a persistência.

### Etapa 6 — Integração com as telas

Adaptamos as telas existentes para consumir os dados do Room, substituindo a dependência de listas fixas em memória.

### Etapa 7 — Gerenciamento de produtos

Criamos a aba Gerenciar, permitindo cadastrar novos produtos, editar registros existentes e excluir itens com confirmação.

### Etapa 8 — Ajustes e validação

Realizamos ajustes na configuração do Gradle e na interface de gerenciamento, incluindo a rolagem da lista e a abertura de formulários em uma janela separada.

## 9. Dificuldades encontradas

Durante o desenvolvimento, encontramos alguns desafios.

### 9.1. Configuração das dependências

Ao integrar o Room ao projeto, tivemos um problema na resolução de bibliotecas do Jetpack Compose.

O Gradle apresentava erros relacionados às dependências `androidx.compose:ui` e `androidx.compose:ui-graphics`.

Identificamos que o grupo das dependências estava incorreto no arquivo `libs.versions.toml`.

Após corrigir para `androidx.compose.ui`, conseguimos executar novamente o aplicativo.

### 9.2. Persistência do carrinho

No Trabalho 2, o carrinho armazenava diretamente os objetos dos produtos em uma lista na memória.

Para adaptar essa estrutura ao Room, utilizamos uma entidade específica que guarda o identificador e a quantidade de cada produto.

Essa abordagem também facilita a atualização dos dados e mantém a integridade das informações.

### 9.3. Interface de gerenciamento

Inicialmente, o formulário de cadastro ocupava boa parte da tela, dificultando a navegação pela lista de produtos.

Reorganizamos a interface para utilizar uma lista com rolagem independente e formulários abertos em janelas específicas.

Com isso, o gerenciamento ficou mais prático.

## 10. Como executar o projeto

1. Clone ou baixe este repositório.
2. Abra a pasta principal do projeto no Android Studio.
3. Aguarde a sincronização das dependências do Gradle.
4. Configure o SDK Android necessário para a compilação (API 37).
5. Selecione um emulador ou dispositivo Android compatível (API 24 ou superior).
6. Execute o módulo `app`.
7. Utilize a aba Gerenciar para testar o cadastro, a edição e a exclusão de produtos.

Na primeira inicialização, os produtos de exemplo são cadastrados no banco quando necessário.

Os dados são armazenados localmente no SQLite do dispositivo.

## 11. Funcionamento do CRUD e persistência dos dados

A terceira versão conta com as quatro operações fundamentais do CRUD, integradas ao banco de dados Room.

### Cadastro de produtos — Create

A aplicação permite cadastrar novos produtos informando nome, descrição, preço e categoria. Os dados são inseridos diretamente no banco SQLite por meio do `ProdutoDao`.

Após o cadastro, os novos produtos passam a integrar o cardápio do aplicativo.

### Consulta de produtos — Read

O cardápio apresenta os produtos armazenados no Room, permitindo realizar pesquisas e filtrar os itens por categoria.

Ao selecionar um produto, a tela de detalhes consulta suas informações utilizando o identificador registrado no banco.

### Atualização de produtos — Update

A aba Gerenciar permite selecionar um produto existente e modificar suas informações.

Após salvar, os dados são atualizados no banco e as alterações são refletidas automaticamente nas telas que observam os produtos.

### Exclusão de produtos — Delete

Os produtos podem ser removidos pela aba Gerenciar, mediante confirmação do usuário.

A exclusão é realizada diretamente no banco, removendo o registro da listagem e do cardápio.

### Persistência local com Room

A principal melhoria da V3 foi substituir o armazenamento temporário em memória por um banco de dados SQLite gerenciado pelo Room.

Dessa forma, os produtos cadastrados, suas alterações e os itens do carrinho são mantidos no armazenamento local do dispositivo.

O aplicativo foi estruturado para recuperar essas informações quando é aberto novamente, sem depender de listas fixas para reconstruir os dados.

O teste de persistência consiste em cadastrar e editar um produto, fechar completamente o aplicativo e reabri-lo para conferir se as informações continuam disponíveis. O mesmo procedimento pode ser realizado com a exclusão, verificando que o item removido não reaparece.

As capturas de tela da próxima seção servem para documentar o funcionamento dessas operações.

## 12. Capturas de tela do aplicativo

Nesta seção estão reunidas as principais telas da Lanchonete V3, incluindo as funcionalidades existentes e o gerenciamento de produtos implementado com Room.

### Tela inicial

Apresenta as categorias disponíveis e os produtos em destaque.

<img width="464" height="873" alt="Tela Inicial" src="https://github.com/user-attachments/assets/f1f912ac-51d8-40d9-81ab-686690935e99" />

### Cardápio

Lista os produtos cadastrados, com opções de pesquisa e filtros por categoria.

<img width="475" height="865" alt="Cardápio" src="https://github.com/user-attachments/assets/54fd3d6b-5dd3-4a48-81ec-d3b9953dd974" />

### Detalhes do produto

Exibe as informações do produto consultado no banco a partir do seu identificador.

<img width="467" height="863" alt="Detalhe Produto" src="https://github.com/user-attachments/assets/988d9065-7663-4d54-9807-d8f73931f255" />

### Carrinho de compras

Permite visualizar os produtos selecionados, alterar quantidades e consultar o valor total do pedido.

<img width="467" height="861" alt="Carrinho" src="https://github.com/user-attachments/assets/619832d2-d44e-443a-ad44-34743c5253e9" />

### Gerenciamento de produtos

A nova aba permite visualizar os registros armazenados no Room, cadastrar produtos e acessar as operações de edição e exclusão.

<img width="475" height="859" alt="Gerenciamento" src="https://github.com/user-attachments/assets/56d5ae8c-2fcd-4a90-aa24-01a7c95c5886" />

### Cadastro de produto

Formulário utilizado para inserir um novo produto diretamente no banco de dados.

<img width="458" height="864" alt="Cadastro Produto" src="https://github.com/user-attachments/assets/54d2523c-2820-4361-bb80-05c93c23cf62" />

### Edição de produto

Permite modificar os dados de um produto existente e salvar as alterações no Room.

<img width="471" height="870" alt="Edição Produto" src="https://github.com/user-attachments/assets/c5f5a797-2655-496c-943e-4795393568d3" />

### Exclusão de produto

O aplicativo solicita confirmação antes de remover um produto do banco.

<img width="478" height="869" alt="Exclusão produto" src="https://github.com/user-attachments/assets/f18eeea8-75df-46fe-bc84-910dd5a822ce" />


### Persistência de dados com Room

Uma das principais melhorias da Lanchonete V3 foi a implementação da persistência local utilizando Room e SQLite.

Diferentemente das versões anteriores, em que os produtos e itens do carrinho eram armazenados temporariamente na memória, agora essas informações são registradas em um banco de dados local.

O aplicativo utiliza os DAOs para realizar as operações de cadastro, consulta, edição e exclusão, enquanto o Room gerencia o armazenamento das informações.

Dessa forma, os dados não dependem apenas da execução do aplicativo e podem ser recuperados do banco mesmo após seu encerramento.

A integração com Flow também permite que as alterações realizadas no banco sejam refletidas automaticamente nas telas do aplicativo.


## 13. Escopo e limitações

O projeto utiliza exclusivamente um banco local do dispositivo Android.

Não foram implementados servidor remoto, autenticação, APIs externas ou sincronização em nuvem, pois esses recursos não fazem parte dos requisitos da atividade.

O fluxo de pagamento é demonstrativo e não realiza transações financeiras reais.

## 14. Integrantes e entrega

**Instituição:** Universidade Positivo

**Curso:** Análise e Desenvolvimento de Sistemas (ADS)

**Disciplina:** Desenvolvimento Mobile

**Integrantes:**
- Eduardo Rodrigues Bueno
- Eduardo Serviam Betio Gomes
- Matheus Machado Komninou

**Projeto:** Trabalho 3 — Persistência de Dados com Room

---

*Projeto desenvolvido para fins acadêmicos na Universidade Positivo, como evolução de um aplicativo Android de lanchonete.*
