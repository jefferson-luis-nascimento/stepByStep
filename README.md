# Passo a Passo - Ambiente Desenvolvimento Linux Mint 19.2

### 1. Instalando o Node.js e o NPM
##### Todos os comando é com o terminal aberto
* Instalando o nvm (Gerenciador de Pacotes para escolher versões no Node.JS)
  * Seguir os passos do site do [NVM](
  https://github.com/nvm-sh/nvm#installing-and-updating);
* Após a instalação, excutar o comando 
  ```nvm install 12.14.1``` 
  que é a versão LTS do site do [Node.js](https://nodejs.org/en/) (verificar a versão no site antes de instalar);
* Setar como default a versão instalada rodando o seguinte comando: 
  ```nvm alias default 12.14.1```
* Após instalação, executar os seguintes comandos para confirmar a instalação: 
  ```node -v``` 
  para verificar a versão do Node.js e 
  ```npm -v``` 
para verificar a versão do NPM. 

### 2. Instalando o YARN
* Ir no site do [YARN](https://legacy.yarnpkg.com/en/)
* Executar os seguintes comandos: 
  1. ```curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -```
  
  2. ```echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list```
  
  3. ```sudo apt update && sudo apt install --no-install-recommends yarn```
* Execute ```yarn -v``` para verificar a versão instalada.
### 3. Configurando o Docker
* Seguir o passo a passo do próprio site do [Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/);
### 4. Iniciando a aplicação Node.js
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
  import express from 'express';

  import routes from './routes';

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

  export default new App().server;
  ~~~
* Incluir o seguinte código no arquivo ```server.js``` o :
  ~~~javascript
  import app from './app';

  app.listen(3333);
  ~~~
* Incluir o seguinte código no arquivo ```routes.js```:
  ~~~javascript
  import { Router } from 'express';

  const routes = new Router();

  routes.get('/', (req, res) => {
    return res.json({message: 'Hello World'});
  });

  export default routes;
  ~~~
* Para testar, execute o seguinte comando ```node src/server.js``` e abra o navegador e digite o seguinte endereço: <http://localhost:3000>
* Instalar o [Insomnia](https://insomnia.rest/) para testar outras chamadas a API
* Instalar o sucrase (uitlizar sintaxe ES6 import/export) e o nodemon para usar o auto build ao editar arquivos do projeto
  ```yarn add sucrase nodemon -D```
  * Criar um arquivo na raiz do projeto chamado nodemon.json e incluir o seguinte código:
  ~~~JSON
  {
    "execMap": {
      "js": "node -r sucrase/register"
    }
  }
  ~~~
  * Alterar o arquivo package.json e incluir a seguinte propriedade:
    ~~~JSON
    "scripts": {
      "dev": "nodemon src/server.js",
      "dev:debug": "nodemon --inspect src/server.js"
    },
    ~~~
  * Parar a execução do projeto com ```ctrl + c``` e para executar o projeto usando o nodemon, usar o seguinte comando:
    ```yarn dev```
* Configurar o debug, clicar no ícone do debug na barra de ferramentas esquerda do VS Code;
* Clicar em "create a launch.json file." 
* Escolher Node.js e substituir o conteúdo do arquivo pelo código abaixo:
  ~~~javascript
  {
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
      {
        "type": "node",
        "request": "attach",
        "name": "Launch Program",
        "restart": true,
        "protocol": "inspector"
      }
    ]
  }
  ~~~
* Configurando o Postgres via Docker:
  * Seguir orientações do site https://hub.docker.com/_/postgres ou executar os comandos abaixo:
    ~~~docker
    docker run --name database -e POSTGRES_PASSWORD=docker -p 5432:5432 -d postgres:11
    ~~~
  * Mais alguns comandos para se usar no docker:
  ```docker ps``` - Lista todos os docker em execução;
  ```docker ps -a``` Lista todos os dockers, inclusive os parados;
  ```docker start <nomeDoDocker>``` - Iniciar um docker parado, trocar o conteúdo dentro <> pelo nome do docker que vai ser iniciado;
* Instalar ferramentas de padronização de código, configurando de acordo com o padrão Airbnb:
  * Executar os seguintes comandos:
    ```yarn add eslint -D```
    ```yarn eslint --init```
    * Escolher as seguintes opções:
      * How would you like to use ESLint?
        * To check syntax, find problems, and enforce code style
      * What type of modules does your project use?
        * JavaScript modules (import/export)
      * Which framework does your project use?
        * None of these 
      * Does your project use TypeScript? (y/N)
        * N
      * Where does your code run?
        * ◯ Browser
          ◉ Node
      * How would you like to define a style for your project? 
        * Use a popular style guide
      * Which style guide do you want to follow?
        * Airbnb: https://github.com/airbnb/javascript
      * What format do you want your config file to be in?
        * Javascript
      * Would you like to install them now with npm? (Y/n)
        * Y
  * Excluir o arquivo package-lock.json que foi gerado e executar o comando ```yarn``` para reintalar as dependências;
  * Instalar a extensão do VS Code chamada Eslint e editorConfig
  * Incluir os seguintes propriedades no arquivo settings.json do VS Code (ctrl + shift + p)
    ~~~JSON
    "[javascript]": {
        "editor.codeActionsOnSave": {
            "source.fixAll.eslint": true,
        }
    },
    "[javascriptreact]": {
        "editor.codeActionsOnSave": {
            "source.fixAll.eslint": true,
        }
    }
    ~~~
  * Instalar o prettier e suas dependências com o seguinte comando:
  ```
  yarn add prettier eslint-config-prettier eslint-plugin-prettier -D
  ```
  * Sobreescrever o conteúdo do arquivo .eslintrc.js com o código abaixo
    ~~~javascript
    module.exports = {
      env: {
        es6: true,
        node: true,
      },
      extends: [
        'airbnb-base',
        'prettier',
      ],
      plugins: ['prettier'],
      globals: {
        Atomics: 'readonly',
        SharedArrayBuffer: 'readonly',
      },
      parserOptions: {
        ecmaVersion: 2018,
        sourceType: 'module',
      },
      rules: {
        'prettier/prettier': 'error',
        'class-methods-use-this': 'off',
        'no-param-reassign': 'off',
        'camelcase': 'off',
        'no-unused-vars': ['error', { 'argsIgnorePattern': 'next' }],
      },
    };
    ~~~
  * Criar um arquivo .prettierrc e incluir o seguinte código:
    ~~~JSON
    {
      "singleQuote": true,
      "trailingComma": "es5"
    }
    ~~~
  * Executar o comando abaixo para corrigir auomaticamente todos os arquivos js;
  ~~~javascript
  yarn eslint --fix src --ext .js
  ~~~
  * Criar o arquivo .editorconfig (clicar com botão direito e escolher Generate .editorconfig) e substituir o conteúdo pelo abaixo:
  ```
  root = true

  [*]
  indent_style = space
  indent_size = 2
  charset = utf-8
  trim_trailing_whitespace = true
  insert_final_newline = true
  ```
