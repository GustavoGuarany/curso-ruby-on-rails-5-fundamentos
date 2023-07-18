# Curso Ruby On Rails 5 Fundamentos

### Rails criar um novo projeto no exemplo 'loja'

```console
rails new loja > cd loja > rails server
```
```console
cd loja 
```
```console
rails server
```


### Rotas da aplicação
Por lidarmos com as rotas da aplicação, selecionaremos `config > routes.rb`:

Construiremos uma rota nesse arquivo, primeiramente colocar root to, que apontará para produtos#index

```console
Rails.application.routes.draw do
    root to: "produdos#index"
end
```
Novo endereço é localhost:3000, a ser atendido por `produtos_controller`, e assim o método index acionará a `view` de mesmo nome e as tabelas serão exibidas.

### Controle
Não basta só criar a rota, temos que criar o `controler` 
Primeiramente acessaremos nosso projeto, e em seguida usaremos o comando `rails generate` para gerar um controller de Produtos.

```console
projeto$ rails generate controller Produtos
```

No Visual Studio, veremos que a nova pasta produtos é criada dentro de `app`. Ainda, será criada a classe `produtos_controller`, localizada em `app > controllers`. Essa classe herda algumas regras de ApplicationController

### Criação do banco de dados

```console
projeto$ rails db:create
Resposta: Database 'db/development.sqlite3' already exists
```

### Gerando modelo 
Em Ruby on Rails, um modelo representa uma tabela em seu banco de dados e é um lugar onde você pode definir métodos para interagir com os dados da tabela.

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
Fazer a migração do modelo para o banco de dados

rails db:migrate

### Popular o banco de dados pelo console do rails

```console
projeto$ rails console
```

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
Ou
```console
camiseta = Produto.create nome:"Tenis", descricao:"Bom para corrida", preco:199, quantidade:3
```
Produto é inserido na base de dados. Limpe a tela do terminal por meio do atalho "Command + L" e acionaremos o comando *rails bdconsole*.

Ao acessarmos o SQlite, utilizaremos dois comandos para que visualizemos o conteúdo em formato de tabela, sendo o primeiro *.mode column*, e o segundo, *.headers on*. Por fim, acionaremos o select * from produtos para visualizar o conteúdo de produtos:

```console
projeto$ rails dbconsole
  SQLite version 3.16.0 2016-11-04 19:09:39
  Enter ".help" for usage hints

sqlite> select * from produtos;
  id      nome          descricao   preco   quantidade    created_at    updated_at
  -----  Camiseta azul  bonita      23.76      10         2018-09-17    2018-09-17 13:38:00.580094
```       

### Controller Local que armazene as reponsabilidade do projeto e poder manipular as regras de negócio

A criação de produtos_controller.rb permite a possibilidade de dialogar com a view, desde que chamemos uma função com o mesmo nome deste, portanto na controller invocaremos a função index. Dessa forma, antes da view index ser acessada, primeiramente passaremos por produtos_controller.rb, e dessa forma as configurações feitas serão aplicadas.

A função *index* que criamos exibirá todos os produtos por *nome*. Criaremos uma variável *produtos* que precisará ser acessada dentro da view, e para isso inseriremos um *@*, o que a transformará em uma variável de instância.  As variáveis de instância são usadas para passar informações do controlador para a view.

```console
class ProdutosController < ApplicationController 

    def index
#Ordenando todos os produtos pelo nome e armazenando o resultado na variável de instância @produtos
       @produtos = Produto.order : nome

    end
end
```

### Trazer os produtos que temos da base de dados

passo 1 - Embutir código Ruby na página HTML > crie o arquivo *index.html.erb* em layouts > produtos  
passo 2 - Em produto, ao invés de ele ser exibido diretamente no código HTML, incluiremos Ruby e substituiremos o produto que foi cadastrado por produto.nome e o mesmo valerá para os outros campos. Por fim, fecharemos o loop each utilizando <% end %>.
passo 3 - Percorrer cada produto em @produtos (a variável de instância definida na ação index do controlador). Para cada produto, cria uma nova linha na tabela HTML e preenche as células com o nome, descrição, preço e quantidade do produto.

```console
<tbody>
# Ordenando pelo nome 
<% @produtos.each do |produto| %> #Esta linha inicia um loop através de cada produto na coleção produtos da variael de instância @produtos . O bloco de código que se segue (até encontrar o <% end %>) será repetido para cada produto na coleção.
    <tr>
        <td><% produto.nome %></td> #Criando uma célula na tabela para cada produto. Imprime o valor da propriedade nome do produto atual.
        <td><% produto.descricao %></td>
        <td><% produto.preco %></td>
        <td><% produto.quantidade %></td>
    </tr>
<% end %>
</tbody>
```
Em resumo, esse código percorre cada *produto* em *produtos* e, para cada um, cria uma nova linha na tabela HTML e preenche as células com o *nome*, *descrição*, *preço* e *quantidade* do produto

### Trazendo produtos ordenados pelo preço e limitando para 1 produto

Dialogando com a view através do controller
```console
class ProdutosController < ApplicationController 

    def index
        @produtos = Produto.order : nome
        @produto_com_menor_preco = Produto.order(:preco).limit 1
    end

end
```

Em ´index.hmtl.erb´ copiaremos a tabela inteira, e faremos uma segunda versão que será ordenada por preço que colocaremos abaixo. Exibiremos, portanto, produtos_com_desconto em vez de apenas produtos.

```html
<h3>Produto com menor preço<h3>
    <table>
        <tr>
            <td>Nome<td/>
            <td>Descrição</td>
            <td>Preço</td>
            <td>Quantidade</td>
        </tr>
    </thead>
<tbody>
<% @produtos_com_menor_preco.each do |produto| %>
    <tr>
        <td><%= produto.nome %></td
        <td><%= produto.descricao %></td>
        <td><%= produto.preco %></td>
        <td><%= produto.quantidade %></td>
    </tr>
# código omitido
    </table>
```

### Bootstrap

Acessaremos o link do Twitter Bootstrap Rails, em que encontraremos uma breve documentação que nos orienta em como realizar a instalação. O primeiro passo é trazer a gem twitter-bootstrap-rails para o projeto.

Acessaremos o arquivo Gemfile, que contém todas as bibliotecas que estamos utilizando, e criaremos a nova gem especificada.

source 'https://rubygems.org'
git_source(:github) { |repo| "https://github.com/#{repo}.git" }

```console
ruby '2.5.1'

gem 'twitter-bootstrap-rails'
# Bundle edge Rails instead: gem 'rails', github: 'rails/rails'
gem 'rails', '~> 5.2.1'
# Use sqlite3 as the database for Active Record
gem 'sqlite3'
# Use Puma as the app server
gem 'puma', '~> 3.11'
# Use SCSS for stylesheets
gem 'sass-rails', '~> 5.0'
# Use Uglifier as compressor for JavaScript assets
gem 'uglifier', '>= 1.3.0'
```
Não basta apenas escrevermos essa gem no arquivo, a própria documentação deixa isso claro, e solicita que depois executemos o comando bundle install. Esse comando verificará se todas as dependências do projeto já estão presentes.

No terminal, acessaremos o diretório loja e acionaremos o comando:

```console
Projeto$ bundle install
```
Por fim, a nova gem já está operante. Nós não precisamos estilizar o Bootstrap, podemos aproveitar o formato padrão. Usaremos o comando *rails generate bootstrap: install static* no terminal, e com isto alguns arquivos serão criados para que o framework possa ser utilizado no projeto.

```console
Projeto$ rails generate bootstrap:install static
```
Feito isso, ao acessarmos *localhost:3000* teremos uma mensagem de erro: *Sprockets::FileNotFound in Produtos#index.* 
Algumas gems que utilizamos necessitam que recarreguemos o servidor. No terminal, utilizaremos o atalho "Ctrl + C" para interrompê-lo. Em seguida, subiremos novamente o servidor por meio do comando *rails s*.

Já no navegador, atualizaremos a página e veremos que não há mais a mensagem de erro. Teremos algumas alterações nas tabelas, que ainda não são modificações estéticas satisfatórias.

Existem duas classes do Bootstrap que podemos utilizar em nossas tabelas - você também pode pesquisar outros recursos na documentação desse framework, disponível em seu site oficial .

Importaremos duas classes para o arquivo *index.html.erb*, em *<table>*, *table table-bordered* e *table-hover*. Faremos o mesmo precedimento tanto para a tabela de produtos cadastrados quanto para a de descontos.

```console
<html>
    <body>
        <table class="table table-bordered table-hover">
            <thead>
                <tr>
                    <td>Nome</td>
                    <td>Descricao</td>
                    <td>Preço</td>
                    <td>Quantidade</td>
                </tr>
            </thead>
# código omitido
    </body>
</html>
```
Na documentação do Twitter Bootstrap, no tópico "Navbar", encontraremos uma barra de navegação chamada "Brand name"

```console
<%= nav_bar brand: "We're sooo web 1.0alizr", brand_link: account_daskboard_path %>
```
Em index.html.erb, criaremos a navbar entre as tags <html> e <body>. Daremos o nome da brand de Loja, e o link apontado será root_url, ou seja, a raiz do nosso projeto.

```console
<%= nav_bar brand: "Loja", brand_link: root_url %>
<body>
    <table class="table table-bordered table-hover">
        <thead>
            <tr>
                <td>Nome</td>
                <td>Descricao</td>
                <td>Preço</td>
                <td>Quantidade</td>
            </tr>
        </thead>
# código omitido
</body>
```

Centralizar tabelas 

Antes do corpo do HTML (<body>), criaremos uma <div>, que receberá uma classe do Bootstrap chamada container, lembrando que precisamos fechar essa <div> no final do código.

```console
<html>
    <%= nav_bar brand: "Loja", brand_link: root_url %>
    <div class="container">
    <body>
        <table class="table table-bordered table-hover">
            <thead>
                <tr>
                    <td>Nome</td>
                    <td>Descricao</td>
                    <td>Preço</td>
                    <td>Quantidade</td>
                </tr>
            </thead>
# código omitido
</html>
```
Destaque na frase "Produtos com menor preço". Incluiremos uma classe do Bootstrap associada a uma nova <div>, a alert alert-success, que receberá a função role="alert". Dentro das <h3> que envolvem a mensagem, incluiremos a segunda classe alert-heading. Por fim, fecharemos a <div>.

```console
<div class="alert alert-success" role="alert">
    <h3 class="alert-heading">Produto com desconto</h3>
</div>
<table class="table table-bordered table-hover">
```

