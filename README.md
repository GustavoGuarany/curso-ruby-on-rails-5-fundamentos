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

### Controller -> Local que armazene as reponsabilidade do projeto e poder manipular as regras de negócio

A criação de `produtos_controller.rb` permite a possibilidade de dialogar com a `view`, desde que chamemos uma função com o mesmo nome deste, portanto na controller invocaremos a função `index`. Dessa forma, antes da view `index` ser acessada, primeiramente passaremos por `produtos_controller.rb`, e dessa forma as configurações feitas serão aplicadas.

A função `index` que criamos exibirá todos os produtos por `nome`. Criaremos uma variável `produtos` que precisará ser acessada dentro da `view`, e para isso inseriremos um `@`, o que a transformará em uma variável de instância.  As variáveis de instância são usadas para passar informações do controlador para a view e o `.order` ordenando todos os produtos pelo nome e armazenando o resultado na variável de instância @produtos.

```console
class ProdutosController < ApplicationController 
    def index
       @produtos = Produto.order : nome
    end
end
```

### Trazer os produtos que temos da base de dados

**Passo 1** - Embutir código Ruby na página HTML > crie o arquivo `index.html.erb` em `layouts > produtos` <br>  
**Passo 2** - Em produto, ao invés de ele ser exibido diretamente no código HTML, incluiremos Ruby e substituiremos o produto que foi cadastrado por `produto.nome` e o mesmo valerá para os outros campos. Por fim, fecharemos o loop `each` utilizando `<% end %>`.<br><br>
**Passo 3** - Percorrer cada produto em `@produtos` (a variável de instância definida na ação index do controlador). Para cada produto, cria uma nova linha na tabela HTML e preenche as células com o **nome, descrição, preço e quantidade do produto**.<br>

`<% @produtos.each do |produto| %> ` Esta linha inicia um loop através de cada produto na coleção produtos da variael de instância @produtos. O bloco de código que se segue até encontrar o `<% end %>` será repetido para cada produto na coleção.`

```console
<body>
    <tbody>
        <% @produtos.each do |produto| %> 
            <tr>
                <td><% produto.nome %></td> #Criando uma célula na tabela para cada produto. Imprime o valor da propriedade nome do produto atual.
                <td><% produto.descricao %></td>
                <td><% produto.preco %></td>
                <td><% produto.quantidade %></td>
            </tr>
        <% end %>
    </tbody>
</body>
```
Em resumo, esse código percorre cada `produto` em `produtos` e, para cada um, cria uma nova linha na tabela HTML e preenche as células com o `nome, descrição, preço e quantidade` do produto

### Trazendo produtos ordenados pelo preço e limitando para 1 produto

Dialogando com a `view` através do `controller`

```console
class ProdutosController < ApplicationController 

    def index
        @produtos = Produto.order : nome
        @produto_com_menor_preco = Produto.order(:preco).limit 1
    end

end
```

Em `index.hmtl.erb` copiaremos a tabela inteira, e faremos uma segunda versão que será ordenada por `preço` que colocaremos abaixo. Exibiremos, portanto, `produtos com menor preco` em vez de apenas `produtos`.

```console
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

Importaremos duas classes para o arquivo `index.html.erb`, em `<table>`, `table table-bordered` e `table-hover`. Faremos o mesmo procedimento tanto para a tabela de **produtos** cadastrados quanto para a de **produtos com menor preco**.

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
Na documentação do `Twitter Bootstrap`, no tópico `Navbar`, encontraremos uma barra de navegação chamada `Brand name`

```console
<%= nav_bar brand: "We're sooo web 1.0alizr", brand_link: account_daskboard_path %>
```

Em `index.html.erb`, criaremos a `navbar` entre as tags `<html>` e `<body>`. Daremos o nome da `brand` de `Loja`, e o link apontado será `root_url`, ou seja, a raiz do nosso projeto.

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

### Menu de navegação e organizando as tabelas

Em `index.html.erb` Antes do corpo do HTML `<body>`, criaremos uma `<div>`, que receberá uma classe do Bootstrap chamada `container`, lembrando que precisamos fechar essa `<div>` no final do código.

```console
<html>
    <%= nav_bar brand: "Loja", brand_link: root_url %>
    ´<div class="container">´
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
    </div>
</html>
```
Dar destaque na frase **Produtos com menor preço**. Incluiremos uma classe do Bootstrap associada a uma nova `<div>`, a `alert alert-success`, que receberá a função `role="alert"`. Dentro das `<h3>` que envolvem a mensagem, incluiremos a segunda classe `alert-heading`. Por fim, fecharemos a `<div>`.

```console
<div class="alert alert-success" role="alert">
    <h3 class="alert-heading">Produto com desconto</h3>
</div>
<table class="table table-bordered table-hover">
#resto do código
```
### Cadastrar novos produtos
Em `App > produtos`, criaremos um novo HTML que chamaremos de `new.html`, e incluiremos nele um formulário de cadastramento de produtos

```console
<html>
    <body>
        <form> 
            Nome <input type="text" name="nome"><br/>
            Descrição <textarea name="descricao"></textarea><br/>
            Preço <input type="number" name="preco" step="0.01"><br/>
            Quantidade <input type="number" name="quantidade"><br/>

            <button type="submit">Criar</button> 
        </form>
    </body>
</html>
```
No navegador, digitaremos a URL `localhost:3000/produtos/new`
Obteremos uma mensagem de erro do tipo Routing, afinal não configuramos uma rota para sabermos qual controller lidará com essa requisição. Em `config > roubtes.rb`, criaremos uma rota para buscar nosso formulário por meio do método `Get()`

```console
Rails.application.routes.draw do
    get "produtos/new",to: "produtos#new" 
    root to: "produtos#index"
end
```
Dessa forma, será verificado se em `produtos_controller.rb` há algum método com o mesmo nome `new`. Como não é o caso, Então, a view `new.html` é acessada e exibida. Tudo isso é feito por meio do método get(). 

### Salvar no Banco
Para criarmos de fato os produtos na base de dados, precisaremos de uma nova requisição, mas dessa vez usaremos o método `post()`. Escreveremos uma requisição do tipo `post()` para `produto`, que será atendida também pelo `controller de produtos`, e como estamos querendo criar um novo produto na base, por convenção do Rails essa função é chamada create em `routes.rb`.

```console
Rails.application.routes.draw do
    post "produtos", to: "produtos#create"
    get "produtos/new", to: "produtos#new"
    root to: "produtos#index"
end
```

Em `produtos_controller` criaremos a ação `create`. Para criarmos um produto no Rails console usávamos `produto.create` e passávamos `nome, descrição, preço` e assim por diante. Faremos o mesmo procedimento aqui, mas dessa vez traremos os valores de parâmetro que estão indo para a URL quando preenchemos o formulário. Para tanto, usaremos o termo `params.require`.

Com isso, fazemos uma requisição desses valores, porém precisamos especificar que queremos resgatar os parâmetros relacionados a produto. Em seguida, usaremos o `permit()` para delimitar quais campos devemos acessar, no caso serão `nome, descricao, preco e quantidade`. Todos esses valores serão armazenados em uma variável interna chamada `produto`.

Incluiremos o `redirect_to root_path`, para ser direcionado automaticamente para a pagina principal 

Também criaremos a função `busca`, passaremos o nome a ser pesquisado "#{nome}", usamos os sinais #{} para que possamos referenciar este nome com a variável @produtos, assim como fizemos em index.html.erb para concatenar o que digitamos em uma string, #{produto.nome}.


```console
class ProdutosController < ApplicationController

    def index
        @produtos = Produto.order(nome: :desc).limit 2
        @produto_com_menor_preco = Produto.order(:preco).limit 1
    end

    def create
        produto = params.require(:produto).permit(:nome, :descricao, :preco, :quantidade)
        Produto.create produto
        redirect_to root_path
    end

    def busca
    nome = params[:nome]
    @produtos = "produtos.where", "#{nome}"

    end
end
```

Para referenciar o produto no formulário, precisaremos inserir código Ruby em `new.html`. Primeiramente, clicaremos sobre esse arquivo e modificaremos seu nome para `new.html.erb`:

No lugar da tag `<form>`. Como o formulário deve estar visível, escreveremos o sinal `=`, e o termo `form_for` para referenciar o formulário, e seu objetivo é criar um novo produto, portanto usaremos `Produto.new`. No final não precisamos utilizar o sinal `=`, apenas o `end`. 

```console
<%= form_for Produto.new do |form| %> 
```

Usando `params.require`, como fizemos em `produtos_controller`, precisamos utilizar um padrão para nomear o produto em `new.html.erb`, como por exemplo: `name="produto[nome]"`.


Exemplo antes:
```console
Nome <input type="text" name="nome"><br/>
```
Depois
```console
Nome <input type="text" name="produto[nome]"><br/>
```
Existe um `helper` do `Rails` que nos auxilia na criação desses campos de texto, sejam elas do tipo text, textarea ou number. Na documentação do Rails encontraremos detalhes sobre cada um desses helpers. Ao buscarmos o termo "text fied", encontraremos um código Rails que gera um HTML bem parecido como o que estamos utilizando em nosso projeto. 

Utilizando ruby e classe do bootstrap 
`row` Responsável pela criação de uma linha <br>
`<div>` Que englobará essa classe
`col-sm-8` Responsável por atribuir um tamanho a linha

```console
<html>
    <body>
        <%= form_for Produto.new do |form| %>
            <div class="row">
                <div class="col-sm-8">
                    <div class="form-group">
                        <%= form.label :nome %>
                        <%= form.text_field :nome, class:"form-control" %>
                    </div>
                </div>
            </div>
            <div class="row">
                <div class="col-sm-8">
                    <div class="form-group">
                        <%= form.label :descricao %>
                        <%= form.text_area :descricao, class:"form-control", rows:4 %>
                    </div>
                </div>
            </div>
            <div class="row">
                <div class="col-sm-2">
                    <div class="form-group">
                        <%= form.label :preco %>
                        <%= form.number_field :preco, step:0.01, class:"form-control" %>
                    </div>
                </div>
            </div>
            <div class="row">
                <div class="col-sm-2">
                    <div class="form-group">
                        <%= form.label :quantidade %>
                        <%= form.number_field :quantidade, class:"form-control" %>
                    </div>
                </div>
            </div>
            
            <%= form.submit "Criar",class:"btn btn-primary" %>
            <% end %>
        <% end %>
    </body>
</html>
```
Podemos fazer uma pequena refatoração no código de index.html.erb, assim, não precisamos mais utilizar <html> e <body>, pois esse papel já está sendo feito no arquivo application.html.erb. Dessa forma, teremos apenas conteúdos relacionados ao index, o que torna nosso código mais organizado, simplificando a manutenção do mesmo.

```console
<table class= table table-bordered table-hover">
    <thead>
        <tr>
            <td>Nome</td>
            <td>Descrição</td>
            <td>Quantidade</td>
            <td>Preço</td>
        </tr>
    </thead>
<tbody>
# código omitido
</table>
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

### Deletar um produto

Na tabela de produtos que contém `Nome`, `Descrição`, `Preço` e `Quantidade`, será adicionada mais uma coluna que abrigará os botões de `Remover`. Em `index.produtos.erb`, criaremos mais uma `<td>` para este fim. Como não haverá um título para esta coluna, iremos incluir uma outra `<td>` e passaremos a propriedade `cospan="1"`, dessa forma não haverá quebras na estrutura da tabela.

Link que possibilitará a remoção dos produtos, utilizaremos o button_to e importaremos uma classe do CSS chamada btn btn-danger, em seguida passaremos o nome do link `Remover` e o que deve ser removido `produto`, além do método a ser utilizado no protocolo HTTP, `method: delete`. 

Validar a remoção com o termo `data`, inseriremos entre chaves uma mensagem de confirmação, cujo texto será `Tem certeza que deseja remover este produto?`

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
                        <td><%= button_to "Remover", produto, method: :delete,
                        class:"btn btn-danger" ,
                        data: {confirm: "Tem certeza que deseja remover o produto #{produto.nome}?"} %></td>
                        
                    </tr>
                    <% end %>
                </tbody
# código omitido
</table>
```

Em `config > routes.rb`, criaremos uma rota para deletarmos um item da loja. Acionaremos o método `delete()` para coletarmos produtos, e também o `id`, logo, escreveremos `produtos/:id/remover`. 

A requisição será enviada para produtos, e por convenção, quando queremos deletar algo, usamos o termo `#destroy`. Assim, especificaremos que essa linha de código está relacionada `as:` a produto. `delete "produtos/:id/remover", to: "produtos#destroy", as: :produto"`

```console
Rails.application.routes.draw do
  delete "produtos/:id/remover", to: "produtos#destroy", as: :produto
  post "produtos", to: "produtos#create"
  get "produtos/new", to: "produtos#new"
  root to: "produtos#index"
end
```
Em `produtos_controller` criaremos a função responsável pela exclusão de um produto, `destroy`. Coletaremos o `id` do produto e o armazenaremos utilizando o termo `params`. Uma vez que o `id` foi coletado, deletaremos o produto associado a ele, portanto escreveremos `Produto.destroy id`.

Uma vez que conseguimos deletar o produto, seremos redirecionados `redirect` para a página principal `root_url`.

```console
def: create
    produto = params.require(:produto).permit(:nome,
    :descricao, :preco, :quantidade)
    Produto.create produto
    redirect_to root_url
end

def destroy
    id = params[:id]
    Produto.destroy id
    redirect_to root_url
end
```
### Solicitar ao Rails que ele gere os verbos HTTP 

Em `routes.rb` substituiremos:
```console
Rails.application.routes.draw do
  delete "produtos/:id", to: "produtos#destroy", as: :produto
  post "produtos", to: "produtos#create"
  get "produtos/new", to: "produtos#new"
  root to: "produtos#index"
end
```
por:
```console
Rails.application.routes.draw do
    resources :produtos, only: [:new, :create, :destroy]
    root to: "produtos#index"

end
```
Dessa forma, serão criadas as rotas que estabelecemos, como podemos visualizar em localhost:3000/rails/info/routes:

Ao tentarmos acessar nosso projeto novamente por meio do endereço localhost:3000, teremos a mensagem de erro `NameError in Produtos#index`.

 O Rails gerou o nome invertido. Para corrigir esse problema, acessaremos `application.html.erb` e substituiremos `produtos_new_path` por `new_produto_path`.

```console
 <body>
<%= nav_bar brand: "Loja", brand_link: root_url do %>
    <%= menu_group do %>
        <%= menu_item "Criar novo produto", new_produto_path %>
    <% end %>
<% end %>
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

No código de `application.html.erb`, ao pesquisarmos um nome, não há mais nenhum valor `nil`.

```console
      <div class="form-group">
        <%= text_field_tag :nome, @nome, class:"form-control"  %>
        <%= button_to  "Buscar", nil, class:"btn btn-default" %>
      </div>
    <% end %>
  <% end %>
    <div class="container">
      <%= yield %>
    </div>
```

Substituiremos `nil` por `@nome` para mantermos o conteúdo digitado visível. Em `produtos.controller.rb`, incluiremos o `@` em `#{nome}`, pois trata-se da variável que estamos trazendo da `view`.

```console
def busca
    @nome = params[:nome]
    @produtos = Produto.where "nome like ?",  "%#{@nome}%"
end
```
Assim feito, acessaremos a página raiz e buscaremos o item "Tv" em nosso buscador. Contudo, seremos surpreendidos pela mensagem de erro Rounting `Error - No route matches` `[POST]` `/produtos/busca`. Quando criamos o formulário em `application.html.erb`, não especificamos o método utilizado no botão, logo, escreveremos `method: :get`.

```console
<%= form_tag busca_produto_path, method: :get, class:"navbar-form navbar-right" do %>
     <div class="form-group">
       <%= text_field_tag :nome, @nome, class:"form-control"  %>
       <%= button_to  "Buscar", nil, class:"btn btn-default" %>
     </div>
```





