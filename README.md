# Curso Ruby On Rails 5 Fundamentos

### Rails criar um novo projeto no exemplo 'loja'

rails new loja

rails server

### De volta ao Visual Studio, clicaremos no diretório app - que armazenará todos os códigos referentes à aplicação - em seguida clicaremos com o botão direito sobre a pasta *views* e selecionaremos a opção "New File". Esse novo arquivo, por padrão, será chamado de index.html.

Construindo uma tabela que conterá as colunas de Nome, Descrição, Preço e Quantidade.
```console
<html>
    <body>
        <table>
            <thread>
                <tr>
                    <td>Nome</td>
                    <td>Descrição</td>
                    <td>Preço</td>
                    <td>Quantidade</td>
                </tr>
            </thead>
        </table>
    </body>
</html>
```

### Criando uma página de produtos e as rotas
passo 1 - Criar uma rota 
passo 2 - em config temos o arquivo routes.rb (Lugar onde conseguimos especificar quem atende aquela requisição)

### Controle
Não basta só criar a rota, temos que criar o controler 
Primeiramente acessaremos nosso projeto, e em seguida usaremos o comando rails generate para gerar um controller de Produtos.

```console
projeto$ rails generate controller Produtos
```

No Visual Studio, veremos que a nova pasta produtos é criada dentro de app. Ainda, será criada a classe produtos_controller, localizada em "app > controllers". Essa classe herda algumas regras de ApplicationController

### Criação do banco de dados

projeto$ rails db:create
Resposta: Database 'db/development.sqlite3' already exists

### Gerando modelo 

```console
projeto$ rails generate model Produto nome:string descricao:text preco:decimal quantidade:integer

Resposta: Running via Spring preloader in process 13111
    invoke active_record
    create db/migrate/20180917133141_create_produtos.rb
    create app/models/produto.rb
    invoke test_unit
    create    test/models/produto_test.rb
    create    test/fixtures/produto.yml.
```

Ao clicarmos na pasta "db > migrate", teremos o arquivo numerogeradopelorails_create_produtos.rb criado:

```console
class CrateProdutos < ActiveRecord::Migration[5.2]
    def change
        create_table :produtos do |t|
         t.string :nome
         t.text :descricao
         t.decimal :preco
         t.integer :quantidade

         t.timestamps
        end
    end
end
```
### Migração

rails db:migrate

### Popular o banco de dados pelo console do rails

projeto$ rails console
```console
> camiseta = Produto.new
=> #<Produto id: nil, nome: nil, descricao: nill ............
> camiseta.nome = "Camiseta Azul"
=> "Camiseta azul"
> camiseta.descricao = "bonita"
=> "bonita"
> camiseta.preco = 23.76
=> 23.76
> camiseta.quantidade = 10
=> 10
> camiseta.save
```

Ao acessarmos o SQlite, utilizaremos dois comandos para que visualizemos o conteúdo em formato de tabela, sendo o primeiro .mode column, e o segundo, .hearder on. Por fim, acionaremos o select * from produtos para visualizar o conteúdo de produtos:

```console
projeto$ rails dbconsole
  SQLite version 3.16.0 2016-11-04 19:09:39
  Enter ".help" for usage hints

sqlite> select * from produtos;
  id      nome          descricao   preco   quantidade    created_at    updated_at
  -----  Camiseta azul  bonita      23.76      10         2018-09-17    2018-09-17 13:38:00.580094
```       





