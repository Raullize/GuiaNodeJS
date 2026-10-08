<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=header"/>

# Middleware e Segurança

## O que são Middlewares?

Middlewares são funções que têm acesso ao objeto de requisição (req), ao objeto de resposta (res) e à próxima função middleware no ciclo de requisição-resposta da aplicação. Eles podem executar código, modificar os objetos de requisição e resposta, encerrar o ciclo, ou chamar o próximo middleware.

### Funções de um Middleware:
- **Execução de código**: Executar qualquer código antes da rota final
- **Modificação de objetos**: Alterar requisições e respostas
- **Encerramento do ciclo**: Finalizar o processo de requisição-resposta
- **Chamada do próximo middleware**: Passar o controle para a próxima função middleware

## Tipos de Middlewares no Express

### Middleware de Aplicação
```javascript
const express = require('express');
const app = express();

// Middleware que será executado em todas as requisições
app.use((req, res, next) => {
  console.log(`${req.method} ${req.url} - ${new Date().toISOString()}`);
  next(); // Passa para o próximo middleware ou rota
});

app.get('/', (req, res) => {
  res.send('Página inicial');
});

app.listen(3000);
```

### Middleware de Rota
```javascript
// Middleware que será aplicado apenas a uma rota específica
app.get('/usuarios', (req, res, next) => {
  console.log('Acessando rota de usuários');
  next();
}, (req, res) => {
  res.send('Lista de usuários');
});
```

### Middleware de Manipulação de Erros
```javascript
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).send('Algo deu errado!');
});
```

## Segurança em Aplicações Node.js

### Principais Vulnerabilidades e Proteções

#### 1. Injeção (SQL, NoSQL, Command)
Ocorre quando dados não confiáveis são enviados para um interpretador como parte de um comando.

**Proteção**:
- Utilize consultas parametrizadas ou ORM (Sequelize, Mongoose)
- Valide e sanitize as entradas do usuário
- Use bibliotecas como `express-validator`

```javascript
// Exemplo com MongoDB e validação
const { body, validationResult } = require('express-validator');

app.post('/usuarios',
  // Validação
  body('email').isEmail().normalizeEmail(),
  body('nome').trim().escape(),
  
  // Middleware para verificar erros
  (req, res, next) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }
    next();
  },
  
  // Controlador
  (req, res) => {
    // Dados já validados
    // Continuar com a lógica segura
  }
);
```

#### 2. Falhas de Autenticação
Problemas na implementação de mecanismos que controlam identidade.

**Proteção**:
- Utilize bibliotecas maduras (Passport.js, JWT)
- Implemente bloqueio após tentativas falhas
- Use HTTPS para todas as comunicações
- Armazene senhas com funções de hash seguras (bcrypt)

```javascript
const bcrypt = require('bcrypt');

// Hashear senha
const hashearSenha = async (senha) => {
  const salt = await bcrypt.genSalt(10);
  return await bcrypt.hash(senha, salt);
};

// Verificar senha
const verificarSenha = async (senha, hash) => {
  return await bcrypt.compare(senha, hash);
};
```

#### 3. Cross-Site Scripting (XSS)
Ocorre quando aplicações recebem dados não confiáveis e os enviam para o navegador sem validação.

**Proteção**:
- Use o middleware Helmet
- Escape todas as saídas de dados
- Implemente Content Security Policy (CSP)

```javascript
const helmet = require('helmet');
const app = express();

// Aplicar configurações de segurança do Helmet
app.use(helmet());

// Configurações específicas do CSP
app.use(
  helmet.contentSecurityPolicy({
    directives: {
      defaultSrc: ["'self'"],
      scriptSrc: ["'self'", 'trusted-cdn.com']
    }
  })
);
```

#### 4. Cross-Site Request Forgery (CSRF)
Ataques que forçam o usuário logado a executar ações não intencionais.

**Proteção**:
- Use tokens CSRF
- Verifique o cabeçalho Origin/Referer

```javascript
const csurf = require('csurf');
const cookieParser = require('cookie-parser');

app.use(cookieParser());
app.use(csurf({ cookie: true }));

app.get('/form', (req, res) => {
  // Passa o token CSRF para o template
  res.render('form', { csrfToken: req.csrfToken() });
});

app.post('/processo', (req, res) => {
  // A validação do token CSRF é automática
  // Se for inválido, um erro será lançado
  res.send('Processo concluído');
});
```

#### 5. Exposição de Dados Sensíveis
Falta de proteção adequada de informações sensíveis.

**Proteção**:
- Utilize HTTPS com configuração adequada
- Não armazene dados sensíveis desnecessariamente
- Use bibliotecas de criptografia para dados sensíveis

```javascript
const crypto = require('crypto');

// Criptografar dados
const criptografar = (texto, chave) => {
  const iv = crypto.randomBytes(16);
  const cipher = crypto.createCipheriv('aes-256-cbc', Buffer.from(chave), iv);
  
  let encrypted = cipher.update(texto);
  encrypted = Buffer.concat([encrypted, cipher.final()]);
  
  return iv.toString('hex') + ':' + encrypted.toString('hex');
};

// Descriptografar dados
const descriptografar = (texto, chave) => {
  const textoParts = texto.split(':');
  const iv = Buffer.from(textoParts.shift(), 'hex');
  const encryptedText = Buffer.from(textoParts.join(':'), 'hex');
  const decipher = crypto.createDecipheriv('aes-256-cbc', Buffer.from(chave), iv);
  
  let decrypted = decipher.update(encryptedText);
  decrypted = Buffer.concat([decrypted, decipher.final()]);
  
  return decrypted.toString();
};
```

## Middlewares de Segurança Essenciais

### Helmet - Proteção por Cabeçalhos HTTP
Helmet ajuda a proteger aplicações Express configurando vários cabeçalhos HTTP relacionados à segurança.

```bash
npm install helmet
```

```javascript
const express = require('express');
const helmet = require('helmet');
const app = express();

// Aplicar todos os middlewares do Helmet
app.use(helmet());

// Ou configurações personalizadas
app.use(
  helmet({
    contentSecurityPolicy: {
      directives: {
        defaultSrc: ["'self'"],
        scriptSrc: ["'self'", "'unsafe-inline'"]
      }
    },
    xssFilter: true,
    noSniff: true
  })
);
```

### CORS (Cross-Origin Resource Sharing)

### O que é CORS?
CORS é um mecanismo de segurança que permite que recursos restritos em uma página web sejam acessados por outro domínio fora do domínio ao qual pertence o recurso.

### Instalação
```bash
npm install cors
```

### Configurações Básicas

#### Permitir Todos os Domínios (Não recomendado para produção)
```javascript
const cors = require('cors');
app.use(cors());
```

#### Configuração com Opções Específicas
```javascript
const corsOptions = {
  origin: 'https://meuapp.com',
  methods: ['GET', 'POST', 'PUT', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization'],
  exposedHeaders: ['X-Total-Count'],
  credentials: true,
  maxAge: 3600
};

app.use(cors(corsOptions));
```

### Exemplos de Configurações

#### Múltiplos Domínios
```javascript
const corsOptions = {
  origin: [
    'https://meuapp.com',
    'https://admin.meuapp.com',
    'https://api.meuapp.com'
  ],
  optionsSuccessStatus: 200
};

app.use(cors(corsOptions));
```

#### Função de Validação de Origem
```javascript
const corsOptions = {
  origin: function (origin, callback) {
    const dominiosPermitidos = [
      'https://meuapp.com',
      'https://admin.meuapp.com'
    ];
    
    if (dominiosPermitidos.indexOf(origin) !== -1 || !origin) {
      callback(null, true);
    } else {
      callback(new Error('Bloqueado pelo CORS'));
    }
  }
};

app.use(cors(corsOptions));
```

#### Configuração por Rota
```javascript
// Rota sem CORS
app.get('/api/publica', (req, res) => {
  res.json({ mensagem: 'Endpoint público' });
});

// Rota com CORS específico
app.get('/api/privada', cors(corsOptions), (req, res) => {
  res.json({ mensagem: 'Endpoint privado' });
});
```

### Configurações de Segurança

#### Produção
```javascript
const corsOptions = {
  origin: process.env.ALLOWED_ORIGINS.split(','),
  methods: ['GET', 'POST', 'PUT', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization'],
  credentials: true,
  maxAge: 3600,
  optionsSuccessStatus: 204,
  preflightContinue: false
};

app.use(cors(corsOptions));
```

#### Desenvolvimento
```javascript
const corsOptions = {
  origin: 'http://localhost:3000',
  methods: '*',
  allowedHeaders: '*',
  credentials: true,
  optionsSuccessStatus: 200
};

if (process.env.NODE_ENV === 'development') {
  app.use(cors(corsOptions));
}
```

### Tratamento de Erros CORS
```javascript
app.use((err, req, res, next) => {
  if (err.message === 'Bloqueado pelo CORS') {
    res.status(403).json({
      erro: 'Acesso não permitido para esta origem'
    });
  } else {
    next(err);
  }
});
```

### Boas Práticas
1. **Nunca use `cors()` sem opções em produção**
2. **Sempre especifique origens permitidas**
3. **Use variáveis de ambiente para configurações**
4. **Implemente logs para requisições bloqueadas**
5. **Configure headers adequadamente**
6. **Use HTTPS em produção**

### Headers CORS Importantes
```javascript
const corsOptions = {
  allowedHeaders: [
    'Content-Type',
    'Authorization',
    'X-Requested-With',
    'Accept',
    'Origin'
  ],
  exposedHeaders: [
    'X-Total-Count',
    'Content-Range',
    'X-Content-Range'
  ],
  methods: 'GET,HEAD,PUT,PATCH,POST,DELETE',
  preflightContinue: false,
  optionsSuccessStatus: 204,
  credentials: true
};
```

### Exemplos de Casos de Uso

#### API Pública
```javascript
app.use(cors({
  origin: '*',
  methods: ['GET'],
  maxAge: 86400
}));
```

#### API com Autenticação
```javascript
app.use(cors({
  origin: process.env.CLIENT_URL,
  methods: ['GET', 'POST'],
  allowedHeaders: ['Content-Type', 'Authorization'],
  credentials: true
}));
```

#### API com Múltiplos Clientes
```javascript
const clientes = new Map([
  ['cliente1', { origin: 'https://cliente1.com', methods: ['GET', 'POST'] }],
  ['cliente2', { origin: 'https://cliente2.com', methods: ['GET'] }]
]);

app.use((req, res, next) => {
  const clienteId = req.headers['x-client-id'];
  const cliente = clientes.get(clienteId);
  
  if (cliente) {
    cors({
      origin: cliente.origin,
      methods: cliente.methods
    })(req, res, next);
  } else {
    res.status(403).json({ erro: 'Cliente não autorizado' });
  }
});
```

### Express-Rate-Limit - Proteção contra Ataques de Força Bruta
Limita o número de requisições que um cliente pode fazer em um determinado período.

```bash
npm install express-rate-limit
```

```javascript
const rateLimit = require('express-rate-limit');

// Limite global
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutos
  max: 100, // Limite de 100 requisições por IP
  standardHeaders: true,
  legacyHeaders: false,
  message: 'Muitas requisições deste IP, tente novamente após 15 minutos'
});

// Aplicar a todas as rotas
app.use(limiter);

// Limite específico para rotas de login
const loginLimiter = rateLimit({
  windowMs: 60 * 60 * 1000, // 1 hora
  max: 5, // 5 tentativas
  message: 'Muitas tentativas de login, tente novamente após 1 hora'
});

app.post('/login', loginLimiter, (req, res) => {
  // Lógica de login
});
```

### HPP - Proteção contra Poluição de Parâmetros HTTP
Protege contra ataques de poluição de parâmetros, evitando a substituição de parâmetros por valores maliciosos.

```bash
npm install hpp
```

```javascript
const hpp = require('hpp');

// Aplicar o middleware HPP
app.use(hpp());
```

### Express-Validator - Validação e Sanitização
Middleware para validação e sanitização de dados de entrada.

```bash
npm install express-validator
```

```javascript
const { body, validationResult } = require('express-validator');

app.post(
  '/usuario',
  // Validações
  body('email').isEmail().withMessage('E-mail inválido'),
  body('senha').isLength({ min: 8 }).withMessage('A senha deve ter pelo menos 8 caracteres'),
  body('nome').trim().escape().notEmpty().withMessage('Nome é obrigatório'),
  
  // Verificação dos resultados da validação
  (req, res, next) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }
    next();
  },
  
  // Manipulador da rota
  (req, res) => {
    // Continuar com a lógica da rota
  }
);
```

## Checklist de Segurança para Aplicações Node.js

1. **Utilize HTTPS** em ambiente de produção
2. **Implemente os middlewares de segurança** (Helmet, CORS, etc.)
3. **✅ Valide todas as entradas do usuário** (express-validator)
4. **Limite as taxas de requisição** (express-rate-limit)
5. **Monitore as dependências** (npm audit, Snyk)
6. **Mantenha as dependências atualizadas**
7. **Utilize um processo de gerenciamento** como PM2
8. **Implemente logging de segurança**
9. **Utilize variáveis de ambiente** para configurações sensíveis
10. **Implemente tratamento adequado de erros**

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=footer"/>