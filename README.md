# Passo a Passo - Ambiente Desenvolvimento Linux Mint 19.2

### Atualizando o VS Code via terminal:
  ```
  wget https://vscode-update.azurewebsites.net/latest/linux-deb-x64/stable -O /tmp/code_latest_amd64.deb
  sudo dpkg -i /tmp/code_latest_amd64.deb
  ```

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
* Configurando a estrutura de pastas e arquivos e o Sequelize
  * Na pasta src, criar as seguintes pastas e arquivos: 
    * config
      * database.js
    * database
      * migrations
    * app
      * controllers
      * models
  * Instalar o Sequelize:
    ~~~
    yarn add sequelize
    ~~~
    ~~~
    yarn add sequelize-cli -D
    ~~~
  * Como vai ser usado o postgres como banco de dados, executar os seguintes comandos:
  ```yarn add pg@^7.0.0``` e
  ```yarn add pg-hstore```
  * Criar um arquivo chamado .sequelizerc e incluir o código abaixo ([Material de Apoio](https://sequelize.org/v5/manual/dialects.html)):
    ~~~javascript
    const { resolve } = require('path');

    module.exports = {
      config: resolve(__dirname, 'src', 'config', 'database.js'),
      'models-path': resolve(__dirname, 'src', 'app', 'models'),
      'migrations-path': resolve(__dirname, 'src', 'database', 'migrations'),
      'seeds-path': resolve(__dirname, 'src', 'database', 'seeds'),
    };
    ~~~
* Criando as Migrations:
  * Para criar uma migration para uma tabela chamada users:
    * executar o comando 
    ~~~
    yarn sequelize migration:create --name=create-users
    ~~~
  * no arquivo gerado em src/database/migrations/(*create-users.js), incluir o código abaixo:
  ~~~javascript
    module.exports = {
      up: (queryInterface, Sequelize) => {
        return queryInterface.createTable('users', {
          id: {
            type: Sequelize.INTEGER,
            allowNull: false,
            autoIncrement: true,
            primaryKey: true,
          },
          name: {
            type: Sequelize.STRING,
            allowNull: false,
          },
          email: {
            type: Sequelize.STRING,
            allowNull: false,
            unique: true,
          },
          password_hash: {
            type: Sequelize.STRING,
            allowNull: false,
          },
          created_at: {
            type: Sequelize.DATE,
            allowNull: false,
          },
          updated_at: {
            type: Sequelize.DATE,
            allowNull: false,
          },
        });
      },

      down: queryInterface => {
        return queryInterface.dropTable('users');
      },
    };
  ~~~
  * Executar o comando abaixo para executar as migrations criar a tabela no banco de dados:
    ```
    yarn sequelize db:migrate
    ```
    * (Opcional) Comandos extras para desfazer as migrations:
      * Desfazer a última:
        ```
        yarn sequelize db:migrate:undo
        ```
      * Desfazer a todas:
        ```
        yarn sequelize db:migrate:undo:all
        ```

* Criando os models:
  * Adicionar a dependencia do bcrypt do javascript:
    ```
    yarn add bcryptjs
    ```
  * no diretório src/app/models, criar o arquivo User.js e incluir o seguinte código:
  ~~~javascript
  import Sequelize, { Model } from 'sequelize';
  import bcrypt from 'bcryptjs';

  class User extends Model {
    static init(sequelize) {
      super.init(
        {
          name: Sequelize.STRING,
          email: Sequelize.STRING,
          password: Sequelize.VIRTUAL,
          password_hash: Sequelize.STRING,
        },
        {
          sequelize,
        }
      );

      this.addHook('beforeSave', async user => {
        if (user.password) {
          user.password_hash = await bcrypt.hash(user.password, 8);
        }
      });

      return this;
    }
  }

  export default User;

  ~~~
* Criando o loader dos models:
  * Criar o arquivo index.js na pasta src/database e incluir o seguinte código:
    ~~~javascript
    import Sequelize from 'sequelize';

    import User from '../app/models/User';

    import databaseConfig from '../config/database';

    const models = [User];

    class Database {
      constructor() {
        this.init();
      }

      init() {
        this.connection = new Sequelize(databaseConfig);

        models.map(model => model.init(this.connection));
      }
    }

    export default new Database();
    ~~~
  * Depois, abra o arquivo src/app.js e inclui a seguinte linha de cima antes da declaração da classe e embaixo do import das rotas:
    ~~~javascript
    import './database';
    ~~~
* Criando o controller para o model User:
  * no diretório src/app/controllers, criar um arquivo chamado UserController.js e incluir o seguinte código:
    ~~~javascript
      import User from '../models/User';

      class UserController {
        async index(req, res) {
          return res.json({ index: true });
        }

        async show(req, res) {
          return res.json({ show: true });
        }

        async store(req, res) {
          return res.json({ store: true });
        }

        async update(req, res) {
          return res.json({ update: true });
        }

        async delete(req, res) {
          return res.json({ delete: true });
        }
      }

      export default new UserController();
    ~~~
  * Substituir o conteúdo do arquivo src/routes.js pelo de baixo (Se rodar a aplicação, já é possível testar):
    ~~~javascript
    import { Router } from 'express';

    import UserController from './app/controllers/UserController';

    const routes = new Router();

    routes.get('/users', UserController.show);

    export default routes;
    ~~~
* Implementar a criação do user no banco de dados:
  * Alterar o método store do arquivo src/app/controller/UserController.js pelo de baixo:
    ~~~javascript
    async store(req, res) {
      const userExists = await User.findOne({ where: { email: req.body.email } });

      if (userExists) {
        return res.status(400).json({ error: 'User already exists.' });
      }

      const { id, name, email } = await User.create(req.body);

      return res.json({ id, name, email });
    }
    ~~~
  * Incluir um método POST no arquivo src/routes.js com a chamada desse método:
    ~~~javascript
    routes.post('/users', UserController.store);
    ~~~
* Implementando autenticação JWT:
  * Instalar a dependencia jsonwebtoken:
    ```
    yarn add jsonwebtoken
    ```
  * Criar o controller SessionController.js no diretório src/app/controllers e incluir o seguinte código:
    ~~~javascript
    import jwt from 'jsonwebtoken';

    import User from '../models/User';

    import authConfig from '../../config/auth';

    class SessionController {
      async store(req, res) {
        const { email, password } = req.body;

        const user = await User.findOne({ where: { email } });

        if (!user) {
          return res.status(401).json({ error: 'User not found.' });
        }

        if (!(await user.checkPassword(password))) {
          return res.status(401).json({ error: 'Password does not match.' });
        }

        const { id, name } = user;

        return res.json({
          user: {
            id,
            name,
            email,
          },
          token: jwt.sign({ id }, authConfig.secret, {
            expiresIn: authConfig.expiresIn,
          }),
        });
      }
    }

    export default new SessionController();

    ~~~
  * Criar o arquivo auth.js em src/config e incluir o seguinte código:
    ~~~javascript
    export default {
      secret: '', // palavra chave pra validar o token, de preferência, colocar uma palavra difícil, ir em md5online.org e gerar por lá
      expiresIn: '7d',
    };
    ~~~
  * Incluir os seguintes códigos no arquivo src/routes.js:
    * Embaixo do import do UserController:
      ~~~javascript
      import SessionController from './app/controllers/SessionController';
      ~~~
    * Embaixo da chamada do UserController.store:
      ~~~javascript
      routes.post('/sessions', SessionController.store);
      ~~~
* Adicionar um middleware de autenticação:
  * Criar o diretório src/app/middlewares e dentro dele o arquivo auth.js e incluir o código abaixo:
    ~~~javascript
      import jwt from 'jsonwebtoken';

      import { promisify } from 'util';

      import authConfig from '../../config/auth';

      export default async (req, res, next) => {
        const authHeader = req.headers.authorization;

        if (!authHeader) {
          return res.status(401).json({ error: 'Token not provided.' });
        }

        const [, token] = authHeader.split(' ');

        try {
          const decoded = await promisify(jwt.verify)(token, authConfig.secret);

          req.userId = decoded.id;

          return next();
        } catch (error) {
          return res.status(401).json({ error: 'Token invalid.' });
        }
      };
    ~~~
  * No arquivo src/routes.js, incluir as seguintes linhas:
    * Após os imports dos controllers, adicionar a linha abaixo:
      ~~~javascript
      import authMiddlware from './app/middlewares/auth';
      ~~~
    * Abaixo da rota de POST de session e antes da rota PUT do user, incluir a seguinte linha:
      ~~~javascript
      routes.use(authMiddlware);
      ~~~
  * Subistituir o conteudo do método update no src/app/controllers/UserController.js pelo código abaixo:
    ~~~javascript
    async update(req, res) {
      const { email, oldPassword } = req.body;

      const user = await User.findByPk(req.userId);

      if (email && email !== user.email) {
        const userExists = await User.findOne({
          where: { email },
        });

        if (userExists) {
          return res.status(400).json({ error: 'User already exists.' });
        }
      }

      if (oldPassword && !(await user.checkPassword(oldPassword))) {
        return res.status(400).json({ error: 'Old password does not match.' });
      }

      const { id, name } = await user.update(req.body);

      return res.json({ id, name, email });
    }
    ~~~
* Implamentar validações com Yup:
  * Instalar a dependencia yup:
    ```
    yarn add yup
    ```
  * Subistituir o conteudo no arquivo src/app/controllers/UserController.js pelo código abaixo:
    ~~~javascript
    import * as Yup from 'yup';

    import User from '../models/User';

    class UserController {
      async index(req, res) {
        return res.json({ index: true });
      }

      async show(req, res) {
        return res.json({ show: true });
      }

      async store(req, res) {
        const schema = Yup.object().shape({
          name: Yup.string().required(),
          email: Yup.string()
            .email()
            .required(),
          password: Yup.string()
            .min(6)
            .required(),
        });

        if (!(await schema.isValid(req.body))) {
          return res.status(400).json({ error: 'Validation fails.' });
        }

        const userExists = await User.findOne({ where: { email: req.body.email } });

        if (userExists) {
          return res.status(400).json({ error: 'User already exists.' });
        }

        const { id, name, email } = await User.create(req.body);

        return res.json({ id, name, email });
      }

      async update(req, res) {
        const schema = Yup.object().shape({
          name: Yup.string(),
          email: Yup.string().email(),
          oldPassword: Yup.string().min(6),
          password: Yup.string()
            .min(6)
            .when('oldPassword', (oldPassword, field) =>
              oldPassword ? field.required() : field
            ),
          confirmPassword: Yup.string().when('password', (password, field) =>
            password ? field.required().oneOf([Yup.ref('password')]) : field
          ),
        });

        if (!(await schema.isValid(req.body))) {
          return res.status(400).json({ error: 'Validation fails.' });
        }

        const { email, oldPassword, password } = req.body;

        const user = await User.findByPk(req.userId);

        if (email && email !== user.email) {
          const userExists = await User.findOne({
            where: { email },
          });

          if (userExists) {
            return res.status(400).json({ error: 'User already exists.' });
          }
        }

        if (!oldPassword && password) {
          return res.status(400).json({ error: 'Old password not provided.' });
        }

        if (oldPassword && !(await user.checkPassword(oldPassword))) {
          return res.status(400).json({ error: 'Old password does not match.' });
        }

        const { id, name } = await user.update(req.body);

        return res.json({ id, name, email });
      }
    }

    export default new UserController();
    ~~~
### 5. Enviando email pelo node.js
* Instalar o nodemailer, express-handlebars e nodemailer-express-handlebars:
  ```
  yarn add nodemailer express-handlebars nodemailer-express-handlebars
  ```
* Criar o arquivo mail.js no diretório src/config com o seguinte código:
  ~~~javascript
  export default {
    host: 'smtp.mailtrap.io',
    port: 2525,
    auth: {
      user: '', /// seu usuario
      pass: '', /// sua senha
    },
    secure: false,
    default: {
      from: 'Equipe FastFeet <noreply@fastfeet.com.br>',
    },
  };
  ~~~
* Criar os seguintes diretórios e arquivos dentro do diretório app:
  * Criar o diretório views
    * Dentro dele, criar o diretório emails
      * Dentro dele, os diretórios layouts e partials e o arquivo templateMail.hbs
        * Dentro de layouts, criar o arquivo default.hbs e incluir o código abaixo:
          ~~~html
          <div style="font-family: Arial, Helvetica, sans-serif; font-size: 16px; line-height: 1.6; color: #222; max-width: 600px">
            {{{ body }}}
            {{> footer }}
          </div>
          ~~~
        * Dentro de partials, criar o arquivo footer.hbs com o seguinte código:
          ~~~html
          <br />
          Equipe FastFeet
          ~~~
    * Dentro do arquivo templateMail.hbs, incluir o seguinte código:
      ~~~html
      <strong>Olá, {{ deliveryman }}</strong>
      <p>Hove uma nova entrega, confira os detalhes abaixo:</p>
      <p>
        <strong>Destinatário: </strong> {{ recipient }} <br />
        <strong>Data/hora: </strong> {{ date }} <br />
        <br />
        <small>
          Vá até o loja para retirar sua entrega.
        </small>
      </p>
      ~~~
* Criar um diretório em src chamado lib e dentro um arquivo chamado Mail.js (src/lib/Mail.js) e incluir o código abaixo:
  ~~~javascript
  import nodemailer from 'nodemailer';
  import { resolve } from 'path';
  import exphbs from 'express-handlebars';
  import nodemailerhbs from 'nodemailer-express-handlebars';

  import mailConfig from '../config/mail';

  class Mail {
    constructor() {
      const { host, port, secure, auth } = mailConfig;
      this.transporter = nodemailer.createTransport({
        host,
        port,
        secure,
        auth: auth.user ? auth : null,
      });

      this.configureTemplates();
    }

    configureTemplates() {
      const viewPath = resolve(__dirname, '..', 'app', 'views', 'emails');

      this.transporter.use(
        'compile',
        nodemailerhbs({
          viewEngine: exphbs.create({
            layoutsDir: resolve(viewPath, 'layouts'),
            partialsDir: resolve(viewPath, 'partials'),
            defaultLayout: 'default',
            extname: '.hbs',
          }),
          viewPath,
          extName: '.hbs',
        })
      );
    }

    sendMail(message) {
      return this.transporter.sendMail({
        ...mailConfig.default,
        ...message,
      });
    }
  }

  export default new Mail();
  ~~~
* Configurar fila de envio de email com o Redis e Bee Queue:
  * Instalando Redis via docker:
    ```
    docker run --name redis -p 6379:6379 -d -t redis:alpine
    ```
  * Instalando o Bee Queue:
    ```
    yarn add bee-queue
    ```
  * Criar o arquivo redis.js em src/config com o seguinte conteúdo:
    ~~~javascript
    export default {
      host: '127.0.0.1',
      port: 6379,
    };
    ~~~
  * Criar o arquivo Queue.js em src/lib com o seguinte conteúdo:
    ~~~javascript
    import Bee from 'bee-queue';
    import DeliveryMail from '../app/jobs/DeliveryMail';

    import redisConfig from '../config/redis';

    const jobs = [DeliveryMail];

    class Queue {
      constructor() {
        this.queues = {};

        this.init();
      }

      init() {
        jobs.forEach(({ key, handle }) => {
          this.queues[key] = {
            bee: new Bee(key, {
              redis: redisConfig,
            }),
            handle,
          };
        });
      }

      add(queue, job) {
        return this.queues[queue].bee.createJob(job).save();
      }

      processQueue() {
        jobs.forEach(job => {
          const { bee, handle } = this.queues[job.key];

          bee.on('failed', this.handleFailure).process(handle);
        });
      }

      handleFailure(job, err) {
        console.log(`Queue ${job.queue.name}: FAILED`, err);
      }
    }

    export default new Queue();
    ~~~ 
  * Criar o diretório src/app/jobs e em seguida criar o arquivo DeliveryMail.js sem src/app/jobs e incluir o código abaixo:
    ~~~javascript
    import { parseISO, format } from 'date-fns';
    import pt from 'date-fns/locale/pt';

    import Mail from '../../lib/Mail';

    class DeliveryMail {
      get key() {
        return 'DeliveryMail';
      }

      async handle({ data }) {
        const { deliveryman, recipient, createdAt } = data;

        await Mail.sendMail({
          to: `${deliveryman.name} <${deliveryman.email}>`,
          subject: 'Nova entrega',
          template: 'deliveryMail',
          context: {
            deliveryman: deliveryman.name,
            recipient: recipient.name,
            date: format(parseISO(createdAt), "'dia' dd 'de' MMMM', às' H:mm'h'", {
              locale: pt,
            }),
          },
        });
      }
    }

    export default new DeliveryMail();
    ~~~
  * Criar o arquivo queue.js no diretório src e incluir o seguinte código:
    ~~~javascript
    import Queue from './lib/Queue';

    Queue.processQueue();
    ~~~
  * Incluir as seguintes propriedades no arquivo package.json na propriedade "scripts":
    ~~~json
    "queue": "nodemon src/queue.js",
    "queue:debug": "nodemon --inspect src/queue.js"
    ~~~
  * Executar em um terminal o comando abaixo para rodar o serviço de fila:
    ```
    yarn queue
    ```
  * Para fazer a chamada de envio de e-mail, insira o código abaixo na classe que vai ser necessário enviar o email:
  ~~~javascript
  // Colocar os imports onde é feito os outros imports da aplicação
  import DeliveryMail from '../jobs/DeliveryMail';
  import Queue from '../../lib/Queue';

  // Esse é o trecho que tem que ser colocado de acordo com seu algforítimo
  await Queue.add(DeliveryMail.key, { deliveryman, recipient, createdAt });
  ~~~