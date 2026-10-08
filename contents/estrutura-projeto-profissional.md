<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=header"/>

# Estruturando Projetos Node.js de Forma Profissional

## Introdução

Este guia apresenta as melhores práticas e ferramentas essenciais para estruturar projetos Node.js de forma profissional, garantindo qualidade, manutenibilidade e escalabilidade. Uma boa estrutura de projeto é fundamental para o sucesso a longo prazo, facilitando a manutenção, colaboração e escalabilidade da aplicação.

## Gerenciamento de Dependências com NPM

O NPM (Node Package Manager) é fundamental para gerenciar dependências e scripts do projeto. Uma configuração adequada do `package.json` é crucial para:

- Gerenciar versões de dependências
- Definir scripts automatizados
- Configurar o ambiente de desenvolvimento
- Documentar o projeto
- Facilitar a colaboração entre desenvolvedores

### Estrutura Recomendada do package.json

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

## Estrutura de Diretórios

Uma estrutura de diretórios bem organizada é crucial para a manutenibilidade do projeto. Ela deve refletir a arquitetura da aplicação e facilitar a localização de arquivos. Cada diretório tem um propósito específico:

```
projeto/
├── src/
│   ├── config/         # ⚙️ Configurações do projeto (banco de dados, servidor, etc)
│   ├── controllers/    # 🎮 Controladores da aplicação (lógica de roteamento)
│   ├── models/         # 📊 Modelos de dados (estrutura e validação)
│   ├── routes/         # 🛣️ Definição de rotas (endpoints da API)
│   ├── services/       # 🛠️ Lógica de negócios (regras de negócio)
│   ├── middlewares/    # 🔄 Middlewares da aplicação (autenticação, logs, etc)
│   ├── utils/          # 🧰 Funções utilitárias (helpers, constantes)
│   └── index.js        # 🚀 Ponto de entrada da aplicação
├── tests/              # 🧪 Testes automatizados
├── docs/               # 📚 Documentação
├── .env.example        # 🔑 Exemplo de variáveis de ambiente
├── .eslintrc.js        # ✨ Configuração do ESLint
├── .gitignore          # 🚫 Arquivos ignorados pelo Git
├── package.json        # 📦 Configuração do projeto
└── README.md           # 📖 Documentação principal
```

## Configuração de Ambiente

### Variáveis de Ambiente

As variáveis de ambiente são fundamentais para configurar diferentes ambientes (desenvolvimento, produção, teste) sem modificar o código. O arquivo `.env` armazena configurações sensíveis e específicas de cada ambiente:

- **Segurança**: Mantém dados sensíveis fora do código
- **Flexibilidade**: Facilita a mudança entre ambientes
- **Configuração**: Centraliza todas as configurações do projeto

```bash
npm install dotenv
```

Crie um arquivo `.env.example` como template:

```env
# Configurações do Servidor
PORT=3000
NODE_ENV=development

# Configurações do Banco de Dados
DATABASE_URL=mongodb://localhost:27017/database
DB_USER=usuario
DB_PASS=senha

# Configurações de Autenticação
JWT_SECRET=seu_secret_aqui
JWT_EXPIRES_IN=1d

# Configurações de Serviços Externos
API_KEY=sua_chave_aqui
```

### Configuração do ESLint

O ESLint é uma ferramenta essencial para manter a qualidade e consistência do código. Ele ajuda a:

- Identificar erros de sintaxe
- Encontrar problemas de estilo
- Manter padrões de código consistentes
- Melhorar a legibilidade do código

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
    'no-console': 'warn',
    'no-unused-vars': 'error',
    'prefer-const': 'error',
    'quotes': ['error', 'single'],
    'semi': ['error', 'always']
  },
};
```

## Git e Versionamento

### .gitignore

O arquivo `.gitignore` é crucial para:

- Evitar o versionamento de arquivos desnecessários
- Proteger informações sensíveis
- Manter o repositório limpo e organizado
- Evitar conflitos entre desenvolvedores

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

### Git Hooks com Husky

Os Git Hooks são scripts que são executados automaticamente em eventos específicos do Git. O Husky facilita a configuração desses hooks para:

- Garantir qualidade do código antes de commits
- Executar testes automaticamente
- Verificar formatação do código
- Prevenir commits com erros

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

## Documentação

### README.md

O README é a primeira impressão do seu projeto e deve conter:

- **Descrição do projeto**: O que ele faz e por que existe
- **Requisitos do sistema**: Node.js, banco de dados, etc
- **Instalação**: Como configurar o ambiente
- **Configuração**: Como configurar o projeto
- **Como executar**: Comandos para iniciar o projeto
- **Como testar**: Como executar os testes
- **Contribuição**: Como contribuir com o projeto
- **Licença**: Informações sobre a licença

### Documentação de API

A documentação da API é essencial para:

- Facilitar o uso da API por outros desenvolvedores
- Manter um registro das funcionalidades
- Servir como contrato entre frontend e backend
- Automatizar testes e validações

```bash
npm install --save-dev swagger-jsdoc swagger-ui-express
```

## Testes

Os testes são fundamentais para garantir a qualidade e confiabilidade do código. Eles ajudam a:

- Prevenir regressões
- Documentar o comportamento esperado
- Facilitar refatorações
- Melhorar a arquitetura do código

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

## Docker

O Docker permite criar ambientes de desenvolvimento consistentes e isolados. Benefícios:

- Ambiente idêntico para todos os desenvolvedores
- Fácil configuração de serviços dependentes
- Isolamento de dependências
- Reprodução exata do ambiente de produção

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

## Logging e Monitoramento

Um sistema de logging robusto é essencial para:

- Identificar e debugar problemas
- Monitorar o desempenho da aplicação
- Rastrear o fluxo de execução
- Manter histórico de operações

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

## Segurança

A segurança é um aspecto crítico de qualquer aplicação. Boas práticas incluem:

1. **Proteção HTTP** com helmet:
```bash
npm install helmet
```

2. **Rate Limiting** para prevenir ataques:
```bash
npm install express-rate-limit
```

3. ✅ **Validação de Dados** para prevenir injeção:
```bash
npm install express-validator
```

## Performance

Otimizações de performance são essenciais para uma boa experiência do usuário:

1. **Compressão** para reduzir o tamanho das respostas:
```bash
npm install compression
```

2. **Cache** para melhorar o tempo de resposta:
```bash
npm install node-cache
```

3. **CORS** configurado adequadamente:
```bash
npm install cors
```

## Seeds

Os seeds são fundamentais para:

- Popular o banco de dados com dados iniciais
- Configurar o ambiente de desenvolvimento
- Garantir consistência entre ambientes
- Facilitar testes e demonstrações

```bash
npm install --save-dev mongoose-seeder
```

Exemplo de seed:

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

## Coleção do Postman

O Postman é uma ferramenta essencial para desenvolvimento e testes de APIs. Benefícios:

- Padronização das requisições
- Facilidade de compartilhamento
- Documentação da API
- Testes automatizados
- Ambiente de desenvolvimento consistente

### Estrutura Recomendada

```
postman/
├── collections/         # 📋 Coleções de requisições
├── environments/       # ⚙️ Configurações de ambiente
└── tests/             # 🧪 Scripts de teste
```

### Variáveis de Ambiente

Configure variáveis para diferentes ambientes:

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

### Testes Automatizados

Exemplo de testes no Postman:

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

## Conclusão

Seguindo estas práticas e utilizando as ferramentas recomendadas, você estará preparado para desenvolver e manter projetos Node.js profissionais, escaláveis e de alta qualidade. Lembre-se de adaptar estas recomendações às necessidades específicas do seu projeto.

---

**Leia também:**
- [Guia de Clean Code em Node.js](./clean-code-nodejs.md)
- [Documentação Oficial do Node.js](https://nodejs.org/en/docs/)
- [Documentação do Express.js](https://expressjs.com/)

**Links Úteis:**
- [ESLint](https://eslint.org/)
- [Jest](https://jestjs.io/)
- [Docker](https://www.docker.com/)
- [Postman](https://www.postman.com/)

**Dica:** Mantenha sua documentação sempre atualizada e compartilhe conhecimento com a equipe!

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=footer"/>