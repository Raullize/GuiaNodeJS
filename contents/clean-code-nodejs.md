<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=header"/>

# ✨ Clean Code em Node.js

Clean Code (Código Limpo) é um conjunto de práticas e princípios que visam tornar seu código mais legível, manutenível e menos propenso a erros. No contexto do Node.js, essas práticas são especialmente importantes devido à natureza assíncrona e event-driven da plataforma.

## 🤔 Por que código limpo é importante no Node.js?

Um código Node.js bem escrito:
- Facilita o gerenciamento de operações assíncronas
- Melhora a performance e escalabilidade
- Reduz a complexidade do código
- Facilita a manutenção e debugging
- Melhora a colaboração entre desenvolvedores

## 📚 Princípios Fundamentais para Node.js

### 1. 🔤 Nomenclatura Clara e Significativa

A nomenclatura é fundamental para a legibilidade do código. No Node.js, é importante usar nomes que revelem claramente a intenção e o propósito de cada componente, especialmente em um ambiente assíncrono onde a clareza é crucial para o entendimento do fluxo de execução.

**Ruim:**
```javascript
const db = require('./db');
const us = require('./userService');
const h = require('./helpers');
```

**Bom:**
```javascript
const database = require('./database');
const userService = require('./userService');
const helpers = require('./utils/helpers');
```

### 2. 🧩 Funções Assíncronas Bem Estruturadas

No Node.js, o gerenciamento de operações assíncronas é crucial. Funções bem estruturadas devem ser pequenas, focadas e seguir um padrão consistente de tratamento de erros. O uso de Promise.all para operações paralelas e try/catch para tratamento de erros são práticas essenciais.

**Ruim:**
```javascript
async function processUserData(userId) {
  const user = await db.getUser(userId);
  const orders = await db.getOrders(userId);
  const stats = await db.getStats(userId);
  return { user, orders, stats };
}
```

**Bom:**
```javascript
async function processUserData(userId) {
  try {
    const [user, orders, stats] = await Promise.all([
      getUserData(userId),
      getUserOrders(userId),
      getUserStats(userId)
    ]);
    
    return { user, orders, stats };
  } catch (error) {
    logger.error('Erro ao processar dados do usuário:', error);
    throw new Error('Falha ao processar dados do usuário');
  }
}

async function getUserData(userId) {
  return await database.getUser(userId);
}

async function getUserOrders(userId) {
  return await database.getOrders(userId);
}

async function getUserStats(userId) {
  return await database.getStats(userId);
}
```

### 3. 🧪 Middlewares Limpos e Focados

Middlewares no Express.js devem ter uma única responsabilidade e serem facilmente testáveis. A separação de preocupações e o tratamento adequado de erros são fundamentais para manter o código limpo e manutenível.

**Ruim:**
```javascript
app.use((req, res, next) => {
  if (req.headers.authorization) {
    const token = req.headers.authorization.split(' ')[1];
    const decoded = jwt.verify(token, 'secret');
    req.user = decoded;
    next();
  } else {
    res.status(401).send('Unauthorized');
  }
});
```

**Bom:**
```javascript
const authMiddleware = (req, res, next) => {
  try {
    const token = extractToken(req);
    const user = verifyToken(token);
    req.user = user;
    next();
  } catch (error) {
    handleAuthError(res, error);
  }
};

function extractToken(req) {
  const authHeader = req.headers.authorization;
  if (!authHeader) {
    throw new Error('Token não fornecido');
  }
  return authHeader.split(' ')[1];
}

function verifyToken(token) {
  return jwt.verify(token, process.env.JWT_SECRET);
}

function handleAuthError(res, error) {
  logger.error('Erro de autenticação:', error);
  res.status(401).json({ error: 'Não autorizado' });
}
```

### 4. 🔍 Tratamento de Erros Consistente

O tratamento de erros em Node.js deve ser consistente e informativo. Um bom tratamento de erros inclui logging adequado, respostas HTTP apropriadas e mensagens claras para o usuário final. O uso de um middleware centralizado de tratamento de erros é uma prática recomendada.

**Ruim:**
```javascript
app.post('/users', async (req, res) => {
  try {
    const user = await User.create(req.body);
    res.json(user);
  } catch (error) {
    console.log(error);
    res.status(500).send('Erro');
  }
});
```

**Bom:**
```javascript
// middleware/errorHandler.js
const errorHandler = (error, req, res, next) => {
  logger.error(error.stack);
  
  if (error instanceof ValidationError) {
    return res.status(400).json({
      error: 'Erro de validação',
      details: error.details
    });
  }
  
  if (error instanceof DatabaseError) {
    return res.status(503).json({
      error: 'Erro no banco de dados',
      message: 'Tente novamente mais tarde'
    });
  }
  
  res.status(500).json({
    error: 'Erro interno do servidor',
    message: process.env.NODE_ENV === 'development' ? error.message : undefined
  });
};

// routes/users.js
router.post('/users', async (req, res, next) => {
  try {
    const user = await User.create(req.body);
    res.status(201).json(user);
  } catch (error) {
    next(error);
  }
});
```

### 5. 📦 Estrutura de Projeto Organizada

Uma estrutura de projeto bem organizada é fundamental para a manutenibilidade e escalabilidade de aplicações Node.js. A separação clara de responsabilidades em diretórios específicos facilita a localização de código, testes e colaboração entre desenvolvedores. Uma estrutura bem definida também ajuda na implementação de novas funcionalidades e na manutenção do código existente.

```
project/
  ├── src/
  │   ├── config/         # Configurações
  │   ├── controllers/    # Controladores
  │   ├── models/         # Modelos
  │   ├── routes/         # Rotas
  │   ├── services/       # Lógica de negócios
  │   ├── middlewares/    # Middlewares
  │   ├── utils/          # Funções utilitárias
  │   └── app.js          # Aplicação principal
  ├── tests/              # Testes
  ├── .env                # Variáveis de ambiente
  └── package.json        # Dependências
```

### 6. 🧠 Promises e Async/Await

O uso correto de Promises e Async/Await é crucial para escrever código assíncrono limpo e legível em Node.js. Essas construções ajudam a evitar o "callback hell" e tornam o código mais fácil de entender e manter. O Async/Await, em particular, permite escrever código assíncrono de forma mais síncrona, melhorando significativamente a legibilidade.

**Ruim:**
```javascript
function getUserData(userId) {
  return db.getUser(userId)
    .then(user => {
      return db.getOrders(user.id)
        .then(orders => {
          return { user, orders };
        });
    })
    .catch(error => {
      console.error(error);
    });
}
```

**Bom:**
```javascript
async function getUserData(userId) {
  try {
    const user = await db.getUser(userId);
    const orders = await db.getOrders(user.id);
    return { user, orders };
  } catch (error) {
    logger.error('Erro ao buscar dados do usuário:', error);
    throw new Error('Falha ao buscar dados do usuário');
  }
}
```

### 7. 📝 Logging Estruturado

O logging estruturado é essencial para debugging e monitoramento em produção. Em vez de usar console.log simples, um sistema de logging bem estruturado deve incluir níveis de severidade, contexto e formato consistente para facilitar a análise e busca de logs. Isso é especialmente importante em ambientes de produção onde o diagnóstico de problemas precisa ser rápido e preciso.

**Ruim:**
```javascript
console.log('Usuário logado:', user);
console.error('Erro:', error);
```

**Bom:**
```javascript
const logger = require('./utils/logger');

logger.info('Usuário logado com sucesso', {
  userId: user.id,
  timestamp: new Date().toISOString()
});

logger.error('Falha na autenticação', {
  error: error.message,
  stack: error.stack,
  userId: req.user?.id
});
```

### 8. 🔐 Segurança e Validação

A validação de dados e implementação de medidas de segurança são cruciais em aplicações Node.js. O uso de middlewares de validação, sanitização de dados e boas práticas de segurança devem ser implementados desde o início do desenvolvimento. Isso inclui validação de entrada, proteção contra ataques comuns e implementação adequada de autenticação e autorização.

**Ruim:**
```javascript
app.post('/users', (req, res) => {
  const user = req.body;
  db.saveUser(user);
  res.send('OK');
});
```

**Bom:**
```javascript
const { body, validationResult } = require('express-validator');

const validateUser = [
  body('email').isEmail().normalizeEmail(),
  body('password').isLength({ min: 8 }),
  body('name').trim().escape(),
  (req, res, next) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }
    next();
  }
];

app.post('/users', validateUser, async (req, res) => {
  try {
    const hashedPassword = await bcrypt.hash(req.body.password, 10);
    const user = await User.create({
      ...req.body,
      password: hashedPassword
    });
    res.status(201).json(user);
  } catch (error) {
    next(error);
  }
});
```

## 🛠️ Ferramentas para Manter o Código Limpo

O uso de ferramentas adequadas é essencial para manter a qualidade e consistência do código em projetos Node.js. Estas ferramentas ajudam a automatizar processos, garantir padrões de código e identificar problemas antes que eles cheguem à produção.

1. **ESLint**: Para análise estática de código
2. **Prettier**: Para formatação consistente
3. **Husky**: Para hooks de git
4. **Jest**: Para testes
5. **SonarQube**: Para análise de qualidade
6. **TypeScript**: Para tipagem estática

## 📋 Checklist de Clean Code para Node.js

Este checklist serve como um guia prático para garantir que seu código Node.js siga as melhores práticas de clean code. Use-o como referência durante o desenvolvimento e em revisões de código para manter a qualidade do seu projeto.

1. **✅ Use async/await consistentemente**
2. **✅ Implemente tratamento de erros adequado**
3. **✅ Mantenha funções pequenas e focadas**
4. **✅ Use nomes descritivos**
5. **✅ Documente APIs e funções complexas**
6. **✅ Implemente logging estruturado**
7. **✅ Valide entradas e saídas**
8. **✅ Mantenha uma estrutura de projeto clara**
9. **✅ Use variáveis de ambiente para configurações**
10. **✅ Implemente testes automatizados**

## 📚 Recursos Adicionais

- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
- [Clean Code JavaScript](https://github.com/ryanmcdermott/clean-code-javascript)
- [Express.js Style Guide](https://github.com/expressjs/express/wiki/Style-guide)

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=footer"/> 