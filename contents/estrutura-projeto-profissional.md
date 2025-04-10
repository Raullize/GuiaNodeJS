<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=header"/>

# 🏗️ Estruturando Projetos Node.js de Forma Profissional

## 🎯 Introdução

Este guia apresenta as melhores práticas e ferramentas essenciais para estruturar projetos Node.js de forma profissional, garantindo qualidade, manutenibilidade e escalabilidade.

## 📦 Gerenciamento de Dependências com NPM

O NPM (Node Package Manager) é fundamental para gerenciar dependências e scripts do projeto. Configure corretamente seu `package.json`:

```bash
npm init -y
```

### 📝 Estrutura Recomendada do package.json

```json
{
  "name": "nome-do-projeto",
  "version": "1.0.0",
  "description": "Descrição do projeto",
  "main": "src/index.js",
  "scripts": {
    "start": "node src/index.js",
    "dev": "nodemon src/index.js",
    "test": "jest",
    "lint": "eslint .",
    "lint:fix": "eslint . --fix"
  },
  "dependencies": {
    // Dependências de produção
  },
  "devDependencies": {
    // Dependências de desenvolvimento
  },
  "engines": {
    "node": ">=18.0.0"
  }
}
```

## 📁 Estrutura de Diretórios

Uma estrutura de diretórios bem organizada é crucial para a manutenibilidade do projeto:

```
projeto/
├── src/
│   ├── config/         # ⚙️ Configurações do projeto
│   ├── controllers/    # 🎮 Controladores da aplicação
│   ├── models/         # 📊 Modelos de dados
│   ├── routes/         # 🛣️ Definição de rotas
│   ├── services/       # 🛠️ Lógica de negócios
│   ├── middlewares/    # 🔄 Middlewares da aplicação
│   ├── utils/          # 🧰 Funções utilitárias
│   └── index.js        # 🚀 Ponto de entrada
├── tests/              # 🧪 Testes automatizados
├── docs/               # 📚 Documentação
├── .env.example        # 🔑 Exemplo de variáveis de ambiente
├── .eslintrc.js        # ✨ Configuração do ESLint
├── .gitignore          # 🚫 Arquivos ignorados pelo Git
├── package.json        # 📦 Configuração do projeto
└── README.md           # 📖 Documentação principal
```

## ⚙️ Configuração de Ambiente

### 🔑 Variáveis de Ambiente

Utilize o pacote `dotenv` para gerenciar variáveis de ambiente:

```bash
npm install dotenv
```

Crie um arquivo `.env.example` como template:

```env
PORT=3000
NODE_ENV=development
DATABASE_URL=mongodb://localhost:27017/database
```

### ✨ Configuração do ESLint

Instale e configure o ESLint para garantir qualidade de código:

```bash
npm install --save-dev eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
```

Exemplo de `.eslintrc.js`:

```javascript
module.exports = {
  env: {
    node: true,
    es2021: true,
  },
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
  ],
  parser: '@typescript-eslint/parser',
  plugins: ['@typescript-eslint'],
  rules: {
    // Regras personalizadas
  },
};
```

## 🔧 Git e Versionamento

### 🚫 .gitignore

Configure um `.gitignore` adequado:

```gitignore
# 📦 Dependências
node_modules/
npm-debug.log
yarn-debug.log
yarn-error.log

# 🔑 Ambiente
.env
.env.local
.env.*.local

# 📝 Logs
logs
*.log

# 💻 IDEs e editores
.idea/
.vscode/
*.swp
*.swo

# 🖥️ Sistema operacional
.DS_Store
Thumbs.db
```

### 🐶 Git Hooks com Husky

Configure hooks do Git para garantir qualidade:

```bash
npm install --save-dev husky lint-staged
```

Configure no `package.json`:

```json
{
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged",
      "pre-push": "npm test"
    }
  },
  "lint-staged": {
    "*.js": ["eslint --fix", "git add"]
  }
}
```

## 📚 Documentação

### 📖 README.md

O README deve conter:

- 📝 Descrição do projeto
- ⚙️ Requisitos do sistema
- 📥 Instalação
- ⚙️ Configuração
- 🚀 Como executar
- 🧪 Como testar
- 🤝 Contribuição
- 📄 Licença

### 📡 Documentação de API

Utilize ferramentas como Swagger ou JSDoc para documentar sua API:

```bash
npm install --save-dev swagger-jsdoc swagger-ui-express
```

## 🧪 Testes

Configure testes automatizados:

```bash
npm install --save-dev jest @types/jest
```

Exemplo de configuração no `package.json`:

```json
{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage"
  },
  "jest": {
    "testEnvironment": "node",
    "coverageDirectory": "./coverage",
    "collectCoverageFrom": [
      "src/**/*.js"
    ]
  }
}
```

## 🐳 Docker

Configure um ambiente de desenvolvimento consistente com Docker:

```dockerfile
# Dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

E um `docker-compose.yml` para serviços adicionais:

```yaml
version: '3'
services:
  app:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - .:/app
    environment:
      - NODE_ENV=development
    depends_on:
      - db

  db:
    image: mongo:latest
    ports:
      - "27017:27017"
```

## 📊 Logging e Monitoramento

Configure um sistema de logging robusto:

```bash
npm install winston
```

Exemplo de configuração:

```javascript
const winston = require('winston');

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' })
  ]
});

if (process.env.NODE_ENV !== 'production') {
  logger.add(new winston.transports.Console({
    format: winston.format.simple()
  }));
}
```

## 🔒 Segurança

Implemente boas práticas de segurança:

1. 🛡️ Use o pacote `helmet` para segurança HTTP:
```bash
npm install helmet
```

2. ⏱️ Implemente rate limiting:
```bash
npm install express-rate-limit
```

3. ✅ Valide entrada de dados:
```bash
npm install express-validator
```

## ⚡ Performance

Otimize a performance do seu aplicativo:

1. 📦 Use compression:
```bash
npm install compression
```

2. 💾 Implemente cache:
```bash
npm install node-cache
```

3. 🌐 Configure CORS adequadamente:
```bash
npm install cors
```

## 🌱 Seeds

No contexto de bancos de dados, seeds são scripts que inserem dados iniciais no banco. Eles são úteis para:

- 📊 Popular o banco de dados com dados de teste
- ⚙️ Configurar o ambiente de desenvolvimento
- 🔄 Garantir consistência entre diferentes ambientes
- 🎯 Facilitar a reprodução de cenários específicos

### 📝 Implementação de Seeds

1. Crie uma pasta `seeds` na raiz do projeto:
```bash
mkdir seeds
```

2. Instale as dependências necessárias:
```bash
npm install --save-dev mongoose-seeder
```

3. Crie um arquivo de seed para cada entidade:
```javascript
// seeds/users.seed.js
const mongoose = require('mongoose');
const User = require('../src/models/User');

const users = [
  {
    name: 'Admin',
    email: 'admin@example.com',
    role: 'admin'
  },
  {
    name: 'User',
    email: 'user@example.com',
    role: 'user'
  }
];

module.exports = {
  model: User,
  documents: users
};
```

4. Adicione um script no `package.json`:
```json
{
  "scripts": {
    "seed": "node seeds/run-seeds.js"
  }
}
```

## 📡 Coleção do Postman

O Postman é uma ferramenta essencial para desenvolvimento e testes de APIs. Uma coleção do Postman é um conjunto de requisições organizadas que pode ser compartilhado entre membros do time.

### ❓ Por que usar coleções do Postman?

- 📋 Padronização das requisições
- 🤝 Facilidade de compartilhamento
- 📚 Documentação da API
- 🧪 Testes automatizados
- ⚙️ Ambiente de desenvolvimento consistente

### 📁 Estrutura Recomendada

```
postman/
├── collections/         # 📋 Coleções de requisições
├── environments/       # ⚙️ Configurações de ambiente
└── tests/             # 🧪 Scripts de teste
```

### 📤 Exportando uma Coleção

1. Abra a coleção desejada no Postman
2. Clique no botão "..." no canto superior direito
3. Selecione "Export"
4. Escolha o formato (Postman Collection v2)
5. Salve o arquivo em `postman/collections/`

### 📥 Importando uma Coleção

1. Abra o Postman
2. Clique em "Importar"
3. Selecione o arquivo da coleção
4. Escolha o formato correto
5. A coleção estará disponível no workspace

### ⚙️ Variáveis de Ambiente

Configure variáveis de ambiente no Postman para diferentes ambientes (dev, staging, prod):

```json
{
  "development": {
    "baseUrl": "http://localhost:3000",
    "apiKey": "dev-key"
  },
  "production": {
    "baseUrl": "https://api.example.com",
    "apiKey": "prod-key"
  }
}
```

### 🧪 Testes Automatizados

Adicione scripts de teste nas requisições:

```javascript
// Teste de status code
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

// Teste de schema
pm.test("Response matches schema", function () {
    const schema = {
        type: "object",
        properties: {
            id: { type: "string" },
            name: { type: "string" }
        }
    };
    pm.expect(pm.response.json()).to.jsonSchema(schema);
});
```

## 🏁 Conclusão

Seguindo estas práticas e utilizando as ferramentas recomendadas, você estará preparado para desenvolver e manter projetos Node.js profissionais, escaláveis e de alta qualidade. Lembre-se de adaptar estas recomendações às necessidades específicas do seu projeto.

---

**📚 Leia também:**
- [Guia de Boas Práticas em Node.js](./boas-praticas-nodejs.md)
- [Documentação Oficial do Node.js](https://nodejs.org/en/docs/)
- [Documentação do Express.js](https://expressjs.com/)

**🔗 Links Úteis:**
- [ESLint](https://eslint.org/)
- [Jest](https://jestjs.io/)
- [Docker](https://www.docker.com/)
- [Postman](https://www.postman.com/)

**💡 Dica:** Mantenha sua documentação sempre atualizada e compartilhe conhecimento com a equipe! 

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=footer"/> 