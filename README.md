# Passo a Passo - Ambiente Desenvolvimento Linux Mint 19.2

### 1. Instalando o Node.JS e o NPM
##### Todos os comando é com o terminal aberto
* Instalando o nvm (Gerenciador de Pacotes para escolher versões no Node.JS)
  * Seguir os passos do site do [NVM](
  https://github.com/nvm-sh/nvm#installing-and-updating);
* Após a instalação, excutar o comando 
  ```nvm install 12.14.1``` 
  que é a versão LTS do site do [Node.JS](https://nodejs.org/en/) (verificar a versão no site antes de instalar);
* Setar como default a versão instalada rodando o seguinte comando: 
  ```nvm alias default 12.14.1```
* Após instalação, executar os seguintes comandos para confirmar a instalação: 
  ```node -v``` 
  para verificar a versão do Node.JS e 
  ```npm -v``` 
para verificar a versão do NPM. 

### 2. Instalando o YARN
* Ir no site do [YARN](https://legacy.yarnpkg.com/en/)
* Executar os seguintes comandos: 
  1. ```curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -```
  
  2. ```echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list```
  
  3. ```sudo apt update && sudo apt install --no-install-recommends yarn```
* Execute ```yarn -v``` para verificar a versão instalada.
### 2. Iniciando a aplicação Node.JS
* Criar um diretório com o nome do Projeto (Trocar a variável nomeDoProjeto pelo nome do projeto que vai ser iniciado): 
  ```mkdir nomeDoProjeto```
* Ir até o diretório criado
  ```cd nomeDoProjeto```
* Executar o comando ```yarn init -y``` para criar o arquivo package.json
* Executar ```code .``` para abrir o VS Code
* Instalar o express
  ```yarn add express```
* Criar um arquivo chamado .gitignore com o seguinte conteúdo:
  ```node_modules```
* Criar um diretório chamado ```src``` (onde vai ficar todo o código fonte do projeto)dentro do diretório raíz do projeto
* Dentro do diretório ```src```, criar os seguintes arquivos: ```app.js```, ```server.js``` e ```routes.js```
* Incluir o seguinte código no arquivo ```app.js```:
  ~~~javascript
  const express = require('express');

  const routes = require('./routes');

  class App {
    constructor() {
      this.server = express();
      
      this.middlewares();
      this.routes();
    }

    middlewares() {
      this.server.use(express.json());
    }

    routes() {
      this.server.use(routes);
    }
  }

  module.exports = new App().server;
  ~~~
* Incluir o seguinte código no arquivo ```server.js``` o :
  ~~~javascript
  const app = require('./app');

  app.listen(3333);
  ~~~
* Incluir o seguinte código no arquivo ```routes.js```:
  ~~~javascript
  const { Router } = require('express');

  const routes = new Router();

  routes.get('/', (req, res) => {
    return res.json({message: 'Hello World'});
  });

  module.exports = routes;
  ~~~
* Para testar, execute o seguinte comando ```node src/server.js``` e abra o navegador e digite o seguinte endereço: <http://localhost:3000>
* Instalar o [Insomnia](https://insomnia.rest/) para testar outras chamadas a API
* Instalar o nodemon para usar o auto build ao editar arquivos do projeto
  ```yarn nodemon -D```
  * Alterar o arquivo package.json e incluir a seguinte propriedade:
    ~~~JSON
    "scripts": {
      "dev": "nodemon src/index.js"
    }
    ~~~
  * Parar a execução do projeto com ```ctrl + c``` e para executar o projeto usando o nodemon, usar o seguinte comando:
    ```yarn dev```


  
