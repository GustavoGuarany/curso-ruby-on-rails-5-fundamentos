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

### Bootstrap

Acessaremos o link do `Twitter Bootstrap Rails`, em que encontraremos uma breve documentação que nos orienta em como realizar a instalação. O primeiro passo é trazer a `gem twitter-bootstrap-rails` para o projeto.

Acessaremos o arquivo `Gemfile`, que contém todas as bibliotecas que estamos utilizando, e criaremos a nova gem especificada incluindo `gem 'twitter-bootstrap-rails'` e `gem 'jquery-rails'` no arquivo Gemfile.

source 'https://rubygems.org'<br>
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
Não basta apenas escrevermos essa `gem` no arquivo, a própria documentação deixa isso claro, e solicita que depois executemos o comando `bundle install`. Esse comando verificará se todas as dependências do projeto já estão presentes.

No terminal, acessaremos o diretório loja e acionaremos o comando:

```console
Projeto$ bundle install
```
Por fim, a nova gem já está operante. Nós não precisamos estilizar o **Bootstrap**, podemos aproveitar o formato padrão. Usaremos o comando `rails generate bootstrap: install static` no terminal, e com isto alguns arquivos serão criados para que o framework possa ser utilizado no projeto.

```console
Projeto$ rails generate bootstrap:install static
```
Feito isso, ao acessarmos `localhost:3000` teremos uma mensagem de erro: `Sprockets::FileNotFound in Produtos#index.` 
Algumas `gems` que utilizamos necessitam que recarreguemos o servidor. No terminal, utilizaremos o atalho `Ctrl + C` para interrompê-lo. Em seguida, subiremos novamente o servidor por meio do comando `rails s`.

Já no navegador, atualizaremos a página e veremos que não há mais a mensagem de erro. Teremos algumas alterações nas tabelas, que ainda não são modificações estéticas satisfatórias.

Existem duas classes do Bootstrap que podemos utilizar em nossas tabelas - você também pode pesquisar outros recursos na documentação desse framework, disponível em seu site oficial .

### Rotas da aplicação
Por lidarmos com as rotas da aplicação, selecionaremos `config > routes.rb`:

linha 1 - `get "produtos/busca", to: "produtos#busca", as: :busca_produto` Esta linha define uma rota `GET` para a URL `produtos/busca`. Quando uma requisição `GET` é enviada para esta `URL`, a aplicação Rails direciona a requisição para a ação `busca` no controlador de produtos `"produtos#busca"`. A opção `as: :busca_produto` define um nome para a rota, o que pode ser usado para gerar o URL correspondente em qualquer parte da aplicação.

linha 2 - `resources :produtos, only: [:new, :create, :destroy]` Esta linha é uma forma abreviada para definir várias rotas relacionadas a recursos de `produtos` de uma só vez. A palavra `resources` é uma função especial no Rails que cria várias rotas padrão `RESTful` para um recurso específico (neste caso, "produtos"). O parâmetro `only: [:new, :create, :destroy]` limita as rotas criadas apenas para as ações `"new", "create" e "destroy"` para o controlador de produtos. Isto significa que as seguintes rotas são criadas:

`GET /produtos/new` (rotas para a ação `new` no controlador de produtos, usada para retornar um formulário para criar um `novo produto`),<br>
`POST /produtos` (rotas para a ação `create` no controlador de produtos, usada para criar um `novo produto`),<br>
`DELETE /produtos/:id` (rotas para a ação `destroy` no controlador de produtos, usada para `deletar um produto`).<br>

linha 3 - `root to: "produtos#index"`: Esta linha define a rota `root` da aplicação, que é a URL que é acessada quando você visita o domínio base da aplicação (por exemplo, www.example.com). Neste caso, a rota `root` está sendo roteada para a ação `index` no controlador de produtos, que geralmente é usado para exibir uma lista de todos os produtos.

```console
Rails.application.routes.draw do
  get "produtos/busca", to: "produtos#busca", as: :busca_produto
  resources :produtos, only: [:new, :create, :destroy]
  root to: "produtos#index"
end
```


### Controle

Não basta só criar a rota, temos que criar o `controler` 
Primeiramente acessaremos nosso projeto, e em seguida usaremos o comando `rails generate` para gerar um controller de Produtos.

A criação de `produtos_controller.rb` permite a possibilidade de dialogar com a `view`, desde que chamemos uma função com o mesmo nome deste, portanto na controller invocaremos a função `index`. Dessa forma, antes da view `index` ser acessada, primeiramente passaremos por `produtos_controller.rb`, e dessa forma as configurações feitas serão aplicadas.

```console
projeto$ rails generate controller Produtos
```

No Visual Studio, veremos que a nova pasta produtos é criada dentro de `app`. Ainda, será criada a classe `produtos_controller`, localizada em `app > controllers`. Essa classe herda algumas regras de ApplicationController

Em `produtos_controller.rb` colocaremos os códigos a seguir

```console
  def index
    @produtos = Produto.order :nome
    @produto_com_desconto = Produto.order(:preco).limit 1
    @baixo_estoque = Produto.where("quantidade < 4").order(:quantidade)
  end

  def create 
    produto = params.require(:produto).permit(:nome, :descricao, :preco, :quantidade)
    Produto.create produto
    redirect_to root_url
    
  end

  def destroy
    id = params[:id]
    Produto.destroy id
    redirect_to root_url
  end

  def busca
      @nome = params[:nome]
      @produtos = Produto.where "nome like ?", "%#{@nome}%"
  end
end
```

`def index`: Este é o início da definição de um método chamado "index". Em um controlador Rails, os métodos correspondem a "ações" que podem ser realizadas na aplicação.

`@produtos = Produto.order :nome` Aqui estamos atribuindo à variável de instância @produtos uma lista de todos os produtos, ordenados pelo campo nome.

`@produto_com_desconto = Produto.order(:preco).limit 1` Aqui estamos buscando o produto com o menor preço e atribuindo-o à variável de instância `@produto_com_desconto`.

`@baixo_estoque = Produto.where("quantidade < 4").order(:quantidade)` Aqui estamos buscando todos os produtos que têm quantidade menor que 4 e ordenando-os por quantidade. O resultado é atribuído à variável @baixo_estoque.
 
### Renderizar os elementos HTML comuns em todas as páginas com application.html.erb

Abra `views > layouts > application.html.erb`  : O application.html.erb é um arquivo de layout padrão no Ruby on Rails. Esse arquivo é responsável por renderizar os elementos HTML comuns em todas as páginas do seu aplicativo, como cabeçalho, rodapé, barra de navegação etc.

```console
<!DOCTYPE html>
<html>
  <head>
    <title>AppFirst</title>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>

    <%= stylesheet_link_tag "application", "data-turbo-track": "reload" %>
    <%= javascript_importmap_tags %>
  </head>

  <body>
    <%= nav_bar brand: "Loja Guga", brand_link: root_url do %>
      <%= menu_group do %>
        <%= menu_item "Criar novo produto", new_produto_path %>
    <% end %>
    
    <%= form_tag busca_produto_path, method: :get, class:"navbar-form" do %>
    <div class="form-group">
      <%= text_field_tag :nome, @nome, class:"form-control" %>
      <%= button_to "Buscar", nil, class:"btn btn-default" %> 
    </div>
    <% end %>
    <% end %>

  <div class="container">
    <%= yield %>
    </div>
  </body>
</html>
```
`<title>AppFirst</title>`: Define o título da página como "AppFirst".<br><br>
`<meta name="viewport" content="width=device-width,initial-scale=1">` : Define as configurações de `viewport` para a página, o que é importante para o design responsivo.<br><br>
`<%= csrf_meta_tags %>`: Isso gera tags de metadados para proteção contra falsificação de solicitação entre sites (CSRF).<br><br>
`<%= csp_meta_tag %>`: Isso gera uma tag de metadados para a Política de Segurança de Conteúdo (CSP).<br><br>
`<%= stylesheet_link_tag "application", "data-turbo-track": "reload" %>`: Isso gera um link para o arquivo de folha de estilo CSS `application` e configura-o para ser recarregado se mudar.<br><br>
`<%= javascript_importmap_tags %>`: Isso gera tags de script para importação de módulos `JavaScript`.<br><br>
`<%= nav_bar brand: "Loja", brand_link: root_url do %>`: Isso cria uma barra de navegação com o nome da marca `Loja` e um link para a URL raiz do aplicativo.<br><br>
`<%= menu_group do %> e <%= menu_item "Criar novo produto", new_produto_path %>`: Criam um grupo de itens de menu com um item `Criar novo produto` que leva ao caminho `new_produto_path`.<br><br>
`<%= form_tag busca_produto_path, method: :get, class:"navbar-form" do %>`: Cria um formulário que, quando enviado, fará uma solicitação `GET` para `busca_produto_path`. O formulário tem a classe CSS `navbar-form`.<br><br>
`<%= text_field_tag :nome, @nome, class:"form-control" %>`: Isso cria um campo de texto com o nome `nome`, o valor `@nome`, e a classe CSS `form-control`.<br><br>
`<%= button_to "Buscar", nil, class:"btn btn-default" %>`: Isso cria um botão com o rótulo `Buscar` e a classe CSS `btn btn-default`.<br>
`<div class="container">`: Cria uma div com a classe CSS `container`.<br><br>

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

Ao clicarmos na pasta `db > migrate`, teremos o arquivo `numerogeradopelorails_create_produtos.rb` criado:

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

```console
rails db:migrate
```

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
> camiseta = Produto.create nome:"Tenis", descricao:"Bom para corrida", preco:199, quantidade:3
```
Produto é inserido na base de dados. Limpe a tela do terminal por meio do atalho `Command + L` e acionaremos o comando `rails bdconsole`.

Ao acessarmos o SQlite, utilizaremos dois comandos para que visualizemos o conteúdo em formato de tabela, sendo o primeiro `.mode column`, e o segundo, `.headers on`. Por fim, acionaremos o `select * from produtos;` para visualizar o conteúdo de produtos:

```console
projeto$ rails dbconsole
  SQLite version 3.16.0 2016-11-04 19:09:39
  Enter ".help" for usage hints

sqlite> select * from produtos;
  id      nome          descricao   preco   quantidade    created_at    updated_at
  -----  Camiseta azul  bonita      23.76      10         2018-09-17    2018-09-17 13:38:00.580094
```       


### Criação da view que corresponde à ação index do controller `ProdutosController`

`view > produtos > index.html.erb`

Dentro de uma aplicação Rails, a convenção é ter um arquivo de `view` correspondente para cada ação definida em um `controller`. Nesse caso, quando a ação index do `ProdutosController` é acionada, o Rails vai renderizar a view `index.html.erb` por padrão.

Por exemplo, se o método index do `ProdutosController` define uma variável `@produtos` que contém uma lista de produtos, você pode usar ERB no arquivo index.html.erb para iterar sobre essa lista e gerar HTML para cada produto.

```console
    <table  class="table table-bordered table-hover">
      <thead>
        <tr>
          <td>Nome</td>
          <td>Descrição</td>
          <td>Preço</td>
          <td>Quantidade</td>
          <td colspan="1">
          
        </tr>
      </thead>
      <tbody> 
      <% @produtos.each do |produto| %>
        <tr>
          <td><%= produto.nome %> </td>
          <td><%= produto.descricao %></td>
          <td><%= produto.preco %></td>
          <td><%= produto.quantidade %></td> 
          <td><%= button_to "Remover", produto, method: :delete, class:"btn btn-danger", data:{confirm: "Tem certeza que deseja remover o produto #{produto.nome}?"} %> 
          
          </td>   
        </tr>
        <% end %>
      </tbody>
    </table>
    <div class="alert alert-success" role="alert">
        <h3 class="alert-heading">Produto com desconto</h3>
    </div>  
    <table class="table table-bordered table-hover" >
      <thead>
        <tr>
          <td>Nome</td>
          <td>Descrição</td>
          <td>Preço</td>
          <td>Quantidade</td>
        </tr>
      </thead>
      <tbody>
      <% @produto_com_desconto.each do |produto| %>
        <tr>
          <td><%= produto.nome %> </td>
          <td><%= produto.descricao %></td>
          <td><%= produto.preco %></td>
          <td><%= produto.quantidade %></td> 
        </tr>
        <% end %>
      </tbody>
    </table>
    </table>
    <div class="alert alert-success" role="alert">
        <h3 class="alert-heading">Produtos com estoque baixo</h3>
    </div>  
    <table class="table table-bordered table-hover" >
      <thead>
        <tr>
          <td>Nome</td>
          <td>Descrição</td>
          <td>Preço</td>
          <td>Quantidade</td>
        </tr>
      </thead>
      <tbody>
      <% @baixo_estoque.each do |produto| %>
        <tr>
          <td><%= produto.nome %> </td>
          <td><%= produto.descricao %></td>
          <td><%= produto.preco %></td>
          <td><%= produto.quantidade %></td> 
        </tr>
        <% end %>
      </tbody>
    </table>
```

`<table class="table table-bordered table-hover">`: Cria uma tabela HTML com as classes CSS 'table', 'table-bordered' e 'table-hover'. Estas classes são usadas para estilizar a tabela.

`<% @produtos.each do |produto| %>`: Este é um loop em Ruby que itera sobre cada `produto` na variável de instância `@produtos`. Para cada `produto`, ele vai renderizar um conjunto de células da tabela `<td>` contendo o `nome, descrição, preço e quantidade do produto.`

`<%= produto.nome %>`: Este código ERB vai imprimir o nome do produto na célula da tabela.

`<%= button_to "Remover", produto, method: :delete, class:"btn btn-danger", data:{confirm: "Tem certeza que deseja remover o produto #{produto.nome}?"} %>`: Cria um botão que, quando clicado, enviará uma solicitação de `DELETE` para o servidor, solicitando a remoção do produto. Antes de enviar a solicitação, será exibido um diálogo de confirmação com a mensagem "Tem certeza que deseja remover o produto #{produto.nome}?".

`<div class="alert alert-success" role="alert"> e <h3 class="alert-heading">Produto com desconto</h3>`: Essas linhas de código criam um cabeçalho indicando que o próximo produto será um produto com desconto.

`<% @produto_com_desconto.each do |produto| %>`: Este é um loop que itera sobre a variável de instância `@produto_com_desconto`. Para cada produto, ele renderiza um conjunto de células da tabela com o `nome, descrição, preço e quantidade do produto`.

O último bloco é semelhante aos blocos anteriores, mas itera sobre a variável de instância `@baixo_estoque`, que contém os produtos com estoque baixo. Para cada produto, ele renderiza um conjunto de células da tabela com o `nome, descrição, preço e quantidade do produto`.

### Novo Produto

Criando um novo formulário HTML de novo produto para a entidade produto. O arquivo `new.html.erb` dentro de `view > produtos` 

Será utilizado para renderizar a view correspondente à ação `new` do controller, onde é exibida a página de criação de um novo recurso, neste caso, um `Novo Produto`.

```console
<%= form_for Produto.new do |form| %>
        <div class="row">
          <div class="col-sm-8">
              <div class="form-group">
                <%= form.label :nome %> 
                <%= form.text_field :nome, class:"form-control"%>
            </div>
          </div>
        </div>

        <div class="row">
          <div class="col-sm-8">
            <div class="form-group">
              <%= form.label :descricao %>
              <%=form.text_area :descricao, class:"form-control", rows:5 %>
            </div>
          </div>
        </div>

        
        <div class="row">
          <div class="col-sm-2">
            <div class="form-group">
            <%= form.label :preco %>
            <%=form.number_field :preco, step:0.01, class:"form-control"%>
            </div>
          </div>
        </div>

        <div class="row">
          <div class="col-sm-2">
            <div class="form-group">
            <%= form.label :quantidade %>
            <%=form.number_field :quantidade, class:"form-control"%>
            </div>
          </div>
        </div>

        <%= form.submit "Criar", class:"btn btn-primary"%>

      <% end %>
```



```console
<%= form_for Produto.new do |form| %> 
```


### Inclusão da Nav bar em todas as páginas e o link Criar produto

Clicaremos sobre o arquivo "views > layouts > application.html.erb", em que há alguns elementos HTML básicos, importações CSS e JavaScript. Esses conteúdos podem ser compartilhados com outras páginas.

Como exemplo, em <body> podemos incluir o código de navbar, e a classe container, pois existe mais abaixo um código yield que irá incluir a informação em todas as páginas do projeto.

Também colocaremos o campo buscar, Primeiramente criaremos um botão `button-to`, como o texto `Buscar`, e o segundo parâmetro com que definiremos o que deve ser enviado com este botão, neste caso, uma tabela vazia nil. Para melhorar o especto visual do botão adicionaremos a classe `btn btn-default`, do Bootstrap: 

```console
<!DOCTYPE html>
<html>
  <head>
    <title>Loja</title>
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>

    <%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
  </head>

  <body>
    <%= nav_bar brand: "Loja", brand_link: root_url do %>
        <%= menu_group do %>
        <%= menu_item "Criar novo produto", produtos_new_path %>
        <% end %>
    <% end %>
    <div class="container">
    <%= yield %>
    </div> 
  </body>
</html>
```


### Criando busca

O próximo passo é imprimir o resultado da busca na tela. Para isso, criaremos um novo HTML em `app > view > produto`. Esse novo arquivo será chamado de `busca.html.erb`, receberá o encaminhamento da controller e exibirá o conteúdo na tela. O código deste arquivo será muito parecido com aquele estruturado em `index.html.erb`, por isso poderemos aproveitar um pouco do código, e ao longo do curso ajustá-lo para que não fiquemos com informações duplicadas.

```console
<table class="table table-bordered table-hover">
    <thead>
        <tr>
            <td>Nome</td>
            <td>Descrição</td>
            <td>Preço</td>
            <td>Quantidade</td>
            <td colspan="1">
        </tr>
    </thead>
    <tbody>
    <% @produtos.each do |produto| %>
        <tr>
            <td><%= produto.nome %></td>
            <td><%= produto.descricao %></td>
            <td><%= produto.preco %></td>
            <td><%= produto.quantidade %></td>
            <td><%= button_to "Remover", produto, method: :delete, class:"btn btn-danger",
            data: {confirm: "Tem certeza que deseja remover o produto #{produto.nome}?"}  %></td>
        </tr>
        <% end %>
    </tbody>
</table>
```







