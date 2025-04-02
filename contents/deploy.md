<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=header"/>

# 🚀 Deploy de Aplicações Node.js

## 🤔 O que é Deploy?
Deploy é o processo de colocar uma aplicação em produção, tornando-a disponível para os usuários finais. Envolve transferir o código da aplicação para um servidor e configurá-lo para execução.

## 📋 Preparação para o Deploy

### 🔍 Checklist antes do Deploy
1. **🧹 Limpar código**: Remover consoles.log desnecessários, comentários de desenvolvimento
2. **🧪 Testes**: Executar testes automatizados para garantir funcionamento
3. **🔒 Variáveis de ambiente**: Configurar corretamente para o ambiente de produção
4. **📊 Logs**: Implementar sistema de logging adequado
5. **⚙️ Scripts de inicialização**: Garantir que scripts de start estejam configurados corretamente
6. **🛠️ Dependências**: Verificar se todas as dependências estão atualizadas e seguras

### 📝 Arquivo package.json
```json
{
  "name": "minha-aplicacao",
  "version": "1.0.0",
  "description": "Aplicação Node.js para produção",
  "main": "server.js",
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js",
    "test": "jest"
  },
  "engines": {
    "node": "16.x"
  },
  "dependencies": {
    "express": "^4.18.2",
    "mongoose": "^6.9.1",
    "dotenv": "^16.0.3"
  },
  "devDependencies": {
    "nodemon": "^2.0.20",
    "jest": "^29.4.2"
  }
}
```

### 🔐 Configuração de variáveis de ambiente
```javascript
// .env.example (para desenvolvimento)
PORT=3000
MONGODB_URI=mongodb://localhost:27017/minha-app
JWT_SECRET=meu_segredo_local
NODE_ENV=development

// Para produção, configure as variáveis no próprio servidor ou plataforma de hospedagem
```

### 📄 Configuração do servidor
```javascript
// server.js
require('dotenv').config();
const express = require('express');
const mongoose = require('mongoose');
const app = express();

// Middleware essenciais
app.use(express.json());

// Configurações específicas para produção
if (process.env.NODE_ENV === 'production') {
  app.use(express.static('client/build')); // Para aplicações full-stack
  
  // Configurações adicionais de segurança para produção
  const helmet = require('helmet');
  app.use(helmet());
}

// Rotas da API
app.use('/api/usuarios', require('./routes/usuarios'));

// Rota para servir a aplicação React em produção
if (process.env.NODE_ENV === 'production') {
  app.get('*', (req, res) => {
    res.sendFile(path.resolve(__dirname, 'client', 'build', 'index.html'));
  });
}

// Conexão com o banco de dados
mongoose.connect(process.env.MONGODB_URI)
  .then(() => console.log('Conectado ao MongoDB'))
  .catch(err => {
    console.error('Erro ao conectar ao MongoDB:', err.message);
    process.exit(1);
  });

// Iniciar o servidor
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Servidor rodando na porta ${PORT}`));
```

## 🚢 Opções de Deploy

### 📦 Serviços de Hospedagem em Nuvem

#### 🌐 Heroku
Plataforma como serviço (PaaS) que facilita o deploy de aplicações Node.js.

**📋 Passos básicos:**
1. Instalar Heroku CLI: `npm install -g heroku`
2. Login: `heroku login`
3. Criar aplicação: `heroku create minha-aplicacao`
4. Configurar variáveis de ambiente: `heroku config:set MONGODB_URI=mongodb://...`
5. Deploy: `git push heroku main`

**📄 Arquivo Procfile:**
```
web: node server.js
```

#### ☁️ Vercel
Ideal para aplicações Next.js e outras frameworks front-end, mas também suporta APIs Node.js.

**📋 Passos básicos:**
1. Instalar Vercel CLI: `npm install -g vercel`
2. Login: `vercel login`
3. Deploy: `vercel`

**📄 Arquivo vercel.json:**
```json
{
  "version": 2,
  "builds": [
    {
      "src": "server.js",
      "use": "@vercel/node"
    }
  ],
  "routes": [
    {
      "src": "/(.*)",
      "dest": "server.js"
    }
  ]
}
```

#### 🌩️ AWS Elastic Beanstalk
Serviço da Amazon para implantação e dimensionamento de aplicativos web.

**📋 Passos básicos:**
1. Instalar AWS EB CLI
2. Inicializar projeto: `eb init`
3. Criar ambiente: `eb create minha-app-producao`
4. Deploy: `eb deploy`

**📄 Arquivo .ebextensions/nodecommand.config:**
```yaml
option_settings:
  aws:elasticbeanstalk:container:nodejs:
    NodeCommand: "npm start"
```

#### 🔵 Microsoft Azure App Service
Serviço de hospedagem de aplicativos web da Microsoft.

**📋 Passos básicos:**
1. Criar recurso App Service no Azure Portal
2. Configurar integração contínua do repositório Git
3. Configurar variáveis de ambiente nos "Configurações de Aplicativo"

#### 🔍 Google Cloud Run
Serviço totalmente gerenciado para implantar e escalar aplicações conteinerizadas.

**📋 Passos básicos:**
1. Containerizar a aplicação com Docker
2. Enviar imagem para Google Container Registry
3. Configurar e implantar via Google Cloud Console ou CLI

### 🐳 Deploy com Docker
Containerizar sua aplicação garante consistência entre ambientes.

**📄 Dockerfile:**
```Dockerfile
FROM node:16-alpine

WORKDIR /app

COPY package*.json ./

RUN npm ci --only=production

COPY . .

ENV NODE_ENV=production

EXPOSE 3000

CMD ["node", "server.js"]
```

**📄 docker-compose.yml:**
```yaml
version: '3'

services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - MONGODB_URI=mongodb://mongo:27017/minha-app
    depends_on:
      - mongo
  
  mongo:
    image: mongo
    volumes:
      - mongo-data:/data/db

volumes:
  mongo-data:
```

**📋 Comandos básicos:**
```bash
# Construir imagem
docker build -t minha-aplicacao-node .

# Executar container
docker run -p 3000:3000 -e MONGODB_URI=mongodb://... minha-aplicacao-node

# Com Docker Compose
docker-compose up -d
```

### 🖥️ VPS (Servidor Virtual Privado)
Mais flexível, mas requer configuração manual e manutenção.

**📋 Provedores populares:**
- DigitalOcean
- Linode
- Vultr
- AWS EC2

**📊 Passos básicos para configurar um VPS:**
1. **📥 Conectar via SSH:**
   ```bash
   ssh root@seu_ip_servidor
   ```

2. **📦 Instalar dependências:**
   ```bash
   apt update && apt upgrade -y
   apt install -y nodejs npm git
   ```

3. **🔒 Criar usuário não-root:**
   ```bash
   adduser nodeuser
   usermod -aG sudo nodeuser
   ```

4. **📂 Clonar repositório:**
   ```bash
   git clone https://github.com/seu-usuario/sua-aplicacao.git
   cd sua-aplicacao
   npm install
   ```

5. **🔄 Configurar PM2 para gestão de processos:**
   ```bash
   npm install -g pm2
   pm2 start server.js --name "minha-app"
   pm2 startup
   pm2 save
   ```

6. **🌐 Configurar Nginx como proxy reverso:**
   ```bash
   apt install -y nginx
   ```

   Criar arquivo de configuração:
   ```nginx
   # /etc/nginx/sites-available/minha-app
   server {
     listen 80;
     server_name meuapp.com www.meuapp.com;

     location / {
       proxy_pass http://localhost:3000;
       proxy_http_version 1.1;
       proxy_set_header Upgrade $http_upgrade;
       proxy_set_header Connection 'upgrade';
       proxy_set_header Host $host;
       proxy_cache_bypass $http_upgrade;
     }
   }
   ```

   Ativar o site:
   ```bash
   ln -s /etc/nginx/sites-available/minha-app /etc/nginx/sites-enabled/
   nginx -t
   systemctl restart nginx
   ```

7. **🔒 Configurar HTTPS com Certbot:**
   ```bash
   apt install -y certbot python3-certbot-nginx
   certbot --nginx -d meuapp.com -d www.meuapp.com
   ```

## 🔄 CI/CD (Integração Contínua / Entrega Contínua)

### 🔄 GitHub Actions
Automatize testes e deploy diretamente do GitHub.

**📄 .github/workflows/deploy.yml:**
```yaml
name: Node.js CI/CD

on:
  push:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    - name: Use Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '16.x'
    - run: npm ci
    - run: npm test
  
  deploy:
    needs: test
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    - name: Deploy to Heroku
      uses: akhileshns/heroku-deploy@v3.12.12
      with:
        heroku_api_key: ${{ secrets.HEROKU_API_KEY }}
        heroku_app_name: "minha-aplicacao"
        heroku_email: ${{ secrets.HEROKU_EMAIL }}
```

### 🔄 GitLab CI/CD
Sistema de CI/CD integrado ao GitLab.

**📄 .gitlab-ci.yml:**
```yaml
stages:
  - test
  - deploy

test:
  stage: test
  image: node:16-alpine
  script:
    - npm ci
    - npm test

deploy_production:
  stage: deploy
  image: ruby:alpine
  script:
    - gem install dpl
    - dpl --provider=heroku --app=minha-aplicacao --api-key=$HEROKU_API_KEY
  only:
    - main
```

### 🔄 Jenkins
Servidor de automação de código aberto.

**📄 Jenkinsfile:**
```groovy
pipeline {
  agent {
    docker {
      image 'node:16-alpine'
    }
  }
  
  stages {
    stage('Build') {
      steps {
        sh 'npm ci'
      }
    }
    
    stage('Test') {
      steps {
        sh 'npm test'
      }
    }
    
    stage('Deploy') {
      when {
        branch 'main'
      }
      steps {
        withCredentials([string(credentialsId: 'HEROKU_API_KEY', variable: 'HEROKU_API_KEY')]) {
          sh 'npm install -g heroku'
          sh 'heroku container:push web -a minha-aplicacao'
          sh 'heroku container:release web -a minha-aplicacao'
        }
      }
    }
  }
}
```

## 📊 Monitoramento e Manutenção

### 🔍 Gerenciamento de Processos com PM2
PM2 é um gerenciador de processos para aplicações Node.js em produção.

**📦 Instalação:**
```bash
npm install -g pm2
```

**📋 Comandos básicos:**
```bash
# Iniciar aplicação
pm2 start server.js --name "minha-app"

# Listar todas as aplicações
pm2 list

# Monitorar recursos
pm2 monit

# Reiniciar aplicação
pm2 restart minha-app

# Visualizar logs
pm2 logs minha-app

# Configurar inicialização automática após reboot
pm2 startup
pm2 save
```

**📄 Arquivo de configuração (ecosystem.config.js):**
```javascript
module.exports = {
  apps: [{
    name: "minha-app",
    script: "server.js",
    instances: "max",
    exec_mode: "cluster",
    env: {
      NODE_ENV: "production",
      PORT: 3000
    },
    env_production: {
      NODE_ENV: "production"
    }
  }]
}
```

### 📊 Logging e Monitoramento

#### 📝 Winston para logging
```javascript
const winston = require('winston');
const logger = winston.createLogger({
  level: 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.json()
  ),
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

// Uso
logger.info('Servidor iniciado na porta 3000');
logger.error('Erro na conexão com o banco de dados', { error: err.message });
```

#### 🔍 New Relic para monitoramento
```bash
npm install newrelic
```

```javascript
// Adicionar no topo do seu arquivo principal
require('newrelic');
```

#### 📈 Sentry para monitoramento de erros
```bash
npm install @sentry/node
```

```javascript
const Sentry = require('@sentry/node');
Sentry.init({ dsn: 'https://examplePublicKey@o0.ingest.sentry.io/0' });

// Middleware para Express
app.use(Sentry.Handlers.requestHandler());
app.use(Sentry.Handlers.errorHandler());

// Capturar erro manualmente
try {
  operacaoComErro();
} catch (e) {
  Sentry.captureException(e);
}
```

## 🧪 Estratégias de Deploy

### 🔄 Blue-Green Deployment
Mantém duas versões idênticas da aplicação (blue e green), alternando entre elas.

**📋 Processo típico:**
1. "Blue" é a versão em produção
2. Implante a nova versão "Green" em outro ambiente
3. Teste a versão "Green"
4. Redirecione o tráfego de "Blue" para "Green"
5. "Green" torna-se produção; "Blue" fica como backup

### 🔄 Canary Deployment
Libera a nova versão gradualmente para um subconjunto de usuários.

**📋 Processo típico:**
1. Nova versão é implantada junto com a versão atual
2. Pequena porcentagem do tráfego é direcionada para a nova versão
3. Monitore métricas e problemas
4. Gradualmente aumente a proporção até 100%

### 🔄 Rolling Updates
Atualiza instâncias da aplicação de forma gradual.

**📋 Processo típico:**
1. Várias instâncias da aplicação estão em execução
2. Atualize uma instância de cada vez
3. Verifique a saúde antes de prosseguir
4. Continue até que todas as instâncias sejam atualizadas

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=footer"/> 