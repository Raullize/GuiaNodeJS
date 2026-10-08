<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=header"/>

# Autenticação e Autorização

## O que é Autenticação e Autorização?

### Autenticação
**Autenticação** é o processo de verificar a identidade de um usuário, confirmando que ele é quem diz ser. É como verificar um documento de identidade antes de permitir a entrada em um local restrito.

### Autorização
**Autorização** ocorre após a autenticação e define o que um usuário autenticado tem permissão para acessar ou fazer no sistema. É como determinar, após verificar sua identidade, quais áreas ou recursos você pode acessar.

## Métodos de Autenticação em Node.js

### Autenticação Baseada em Sessão
Neste método, após a autenticação bem-sucedida, o servidor cria uma sessão para o usuário e armazena os dados da sessão no servidor, enviando apenas um ID de sessão ao cliente via cookie.

#### Implementação com Express-Session
```bash
npm install express-session
```

```javascript
const express = require('express');
const session = require('express-session');
const app = express();

app.use(express.json());
app.use(session({
  secret: 'chave-secreta-da-sessao',
  resave: false,
  saveUninitialized: false,
  cookie: { 
    secure: process.env.NODE_ENV === 'production', // HTTPS em produção
    maxAge: 24 * 60 * 60 * 1000 // 24 horas
  }
}));

// Rota de login
app.post('/login', (req, res) => {
  const { email, senha } = req.body;
  
  // Verificação fictícia (substitua por verificação real com banco de dados)
  if (email === 'usuario@exemplo.com' && senha === 'senha123') {
    // Salva informações do usuário na sessão
    req.session.usuario = {
      id: 1,
      email,
      nome: 'Usuário Exemplo',
      roles: ['usuario']
    };
    
    return res.json({ mensagem: 'Login realizado com sucesso!' });
  }
  
  res.status(401).json({ mensagem: 'Credenciais inválidas' });
});

// Middleware para verificar autenticação
function autenticar(req, res, next) {
  if (req.session.usuario) {
    return next();
  }
  
  res.status(401).json({ mensagem: 'Não autorizado' });
}

// Rota protegida
app.get('/perfil', autenticar, (req, res) => {
  res.json({ usuario: req.session.usuario });
});

// Rota de logout
app.post('/logout', (req, res) => {
  req.session.destroy();
  res.json({ mensagem: 'Logout realizado com sucesso' });
});

app.listen(3000, () => {
  console.log('Servidor rodando na porta 3000');
});
```

### Autenticação Baseada em Token (JWT)
JSON Web Tokens (JWT) são tokens compactos e autônomos que contêm informações sobre o usuário em formato JSON. Eles são assinados digitalmente, garantindo que as informações não sejam alteradas.

#### Implementação com jsonwebtoken
```bash
npm install jsonwebtoken
```

```javascript
const express = require('express');
const jwt = require('jsonwebtoken');
const app = express();

app.use(express.json());

const JWT_SECRET = process.env.JWT_SECRET || 'chave-secreta-nao-use-em-producao';

// Rota de login
app.post('/login', (req, res) => {
  const { email, senha } = req.body;
  
  // Verificação fictícia (substitua por verificação real com banco de dados)
  if (email === 'usuario@exemplo.com' && senha === 'senha123') {
    // Dados a serem armazenados no token
    const payload = {
      id: 1,
      email,
      nome: 'Usuário Exemplo',
      roles: ['usuario']
    };
    
    // Gerar token JWT
    const token = jwt.sign(payload, JWT_SECRET, {
      expiresIn: '24h' // Token expira em 24 horas
    });
    
    return res.json({ 
      mensagem: 'Login realizado com sucesso!',
      token 
    });
  }
  
  res.status(401).json({ mensagem: 'Credenciais inválidas' });
});

// Middleware para verificar token JWT
function autenticarJWT(req, res, next) {
  // Pegar o token do cabeçalho de autorização
  const authHeader = req.headers.authorization;
  
  if (!authHeader || !authHeader.startsWith('Bearer ')) {
    return res.status(401).json({ mensagem: 'Token não fornecido' });
  }
  
  const token = authHeader.split(' ')[1];
  
  try {
    // Verificar e decodificar o token
    const usuario = jwt.verify(token, JWT_SECRET);
    req.usuario = usuario;
    next();
  } catch (erro) {
    if (erro.name === 'TokenExpiredError') {
      return res.status(401).json({ mensagem: 'Token expirado' });
    }
    
    return res.status(403).json({ mensagem: 'Token inválido' });
  }
}

// Rota protegida
app.get('/perfil', autenticarJWT, (req, res) => {
  res.json({ usuario: req.usuario });
});

app.listen(3000, () => {
  console.log('Servidor rodando na porta 3000');
});
```

### Refresh Tokens
Refresh Tokens são usados para obter novos tokens de acesso (access tokens) sem a necessidade de fazer login novamente. Eles têm uma vida útil maior que os tokens de acesso.

```javascript
// Gerar tokens de acesso e refresh
app.post('/login', (req, res) => {
  const { email, senha } = req.body;
  
  // Verificação fictícia
  if (email === 'usuario@exemplo.com' && senha === 'senha123') {
    const usuario = {
      id: 1,
      email,
      nome: 'Usuário Exemplo'
    };
    
    // Token de acesso (curta duração)
    const accessToken = jwt.sign(usuario, JWT_SECRET, { expiresIn: '15m' });
    
    // Refresh token (longa duração)
    const refreshToken = jwt.sign(
      { id: usuario.id },
      JWT_REFRESH_SECRET,
      { expiresIn: '7d' }
    );
    
    // Em produção, armazene o refreshToken em um banco de dados
    refreshTokens.push(refreshToken);
    
    return res.json({
      accessToken,
      refreshToken
    });
  }
  
  res.status(401).json({ mensagem: 'Credenciais inválidas' });
});

// Rota para obter um novo token de acesso usando o refresh token
app.post('/refresh-token', (req, res) => {
  const { refreshToken } = req.body;
  
  if (!refreshToken) {
    return res.status(401).json({ mensagem: 'Refresh token não fornecido' });
  }
  
  // Verificar se o refresh token existe na lista/banco de dados
  if (!refreshTokens.includes(refreshToken)) {
    return res.status(403).json({ mensagem: 'Refresh token inválido' });
  }
  
  try {
    // Verificar e decodificar o refresh token
    const usuario = jwt.verify(refreshToken, JWT_REFRESH_SECRET);
    
    // Gerar novo token de acesso
    const accessToken = jwt.sign(
      { id: usuario.id, nome: usuario.nome },
      JWT_SECRET,
      { expiresIn: '15m' }
    );
    
    return res.json({ accessToken });
  } catch (erro) {
    return res.status(403).json({ mensagem: 'Token inválido' });
  }
});
```

## Autenticação com Passport.js

Passport.js é uma middleware de autenticação para Node.js altamente flexível e modular. Ele suporta diversas estratégias de autenticação por meio de plugins.

### Instalação
```bash
npm install passport passport-local passport-jwt
```

### Implementação de Estratégias

#### Estratégia Local (usuário e senha)
```javascript
const express = require('express');
const passport = require('passport');
const LocalStrategy = require('passport-local').Strategy;
const session = require('express-session');
const app = express();

// Configuração do Express
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Configuração da sessão
app.use(session({
  secret: 'chave-secreta',
  resave: false,
  saveUninitialized: false
}));

// Inicialização do Passport
app.use(passport.initialize());
app.use(passport.session());

// Banco de dados fictício de usuários
const usuarios = [
  {
    id: 1,
    nome: 'Usuário Teste',
    email: 'usuario@teste.com',
    senha: 'senha123'
  }
];

// Implementação da estratégia local
passport.use(new LocalStrategy(
  { usernameField: 'email' },
  (email, senha, done) => {
    // Encontrar usuário no banco
    const usuario = usuarios.find(u => u.email === email);
    
    if (!usuario) {
      return done(null, false, { message: 'Usuário não encontrado' });
    }
    
    // Verificar senha (em produção, use bcrypt)
    if (usuario.senha !== senha) {
      return done(null, false, { message: 'Senha incorreta' });
    }
    
    return done(null, usuario);
  }
));

// Serialização e deserialização de usuários
passport.serializeUser((usuario, done) => {
  done(null, usuario.id);
});

passport.deserializeUser((id, done) => {
  const usuario = usuarios.find(u => u.id === id);
  done(null, usuario);
});

// Rota de login
app.post('/login', passport.authenticate('local'), (req, res) => {
  res.json({ mensagem: 'Login realizado com sucesso', usuario: req.user });
});

// Middleware para verificar autenticação
function estaAutenticado(req, res, next) {
  if (req.isAuthenticated()) {
    return next();
  }
  res.status(401).json({ mensagem: 'Não autorizado' });
}

// Rota protegida
app.get('/perfil', estaAutenticado, (req, res) => {
  res.json({ usuario: req.user });
});

// Rota de logout
app.post('/logout', (req, res) => {
  req.logout(function(err) {
    if (err) return next(err);
    res.json({ mensagem: 'Logout realizado com sucesso' });
  });
});

app.listen(3000, () => {
  console.log('Servidor rodando na porta 3000');
});
```

#### Estratégia JWT
```javascript
const express = require('express');
const passport = require('passport');
const JwtStrategy = require('passport-jwt').Strategy;
const ExtractJwt = require('passport-jwt').ExtractJwt;
const jwt = require('jsonwebtoken');
const app = express();

app.use(express.json());

const JWT_SECRET = 'chave-secreta';

// Banco de dados fictício de usuários
const usuarios = [
  {
    id: 1,
    nome: 'Usuário Teste',
    email: 'usuario@teste.com',
    senha: 'senha123'
  }
];

// Configuração da estratégia JWT
const jwtOptions = {
  jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
  secretOrKey: JWT_SECRET
};

passport.use(new JwtStrategy(jwtOptions, (payload, done) => {
  // Encontrar usuário pelo ID do payload
  const usuario = usuarios.find(u => u.id === payload.id);
  
  if (usuario) {
    return done(null, usuario);
  } else {
    return done(null, false);
  }
}));

// Inicialização do Passport
app.use(passport.initialize());

// Rota de login
app.post('/login', (req, res) => {
  const { email, senha } = req.body;
  
  const usuario = usuarios.find(u => u.email === email);
  
  if (!usuario || usuario.senha !== senha) {
    return res.status(401).json({ mensagem: 'Credenciais inválidas' });
  }
  
  const payload = {
    id: usuario.id,
    email: usuario.email,
    nome: usuario.nome
  };
  
  const token = jwt.sign(payload, JWT_SECRET, { expiresIn: '1h' });
  
  res.json({ token });
});

// Rota protegida com autenticação JWT
app.get('/perfil', passport.authenticate('jwt', { session: false }), (req, res) => {
  res.json({ usuario: req.user });
});

app.listen(3000, () => {
  console.log('Servidor rodando na porta 3000');
});
```

## Controle de Acesso Baseado em Funções (RBAC)

RBAC (Role-Based Access Control) é um sistema onde as permissões são associadas a funções (roles) e os usuários recebem essas funções.

```javascript
const express = require('express');
const jwt = require('jsonwebtoken');
const app = express();

app.use(express.json());

const JWT_SECRET = 'chave-secreta';

// Banco de dados fictício de usuários com funções
const usuarios = [
  {
    id: 1,
    nome: 'Admin',
    email: 'admin@exemplo.com',
    senha: 'admin123',
    roles: ['admin', 'editor', 'usuario']
  },
  {
    id: 2,
    nome: 'Editor',
    email: 'editor@exemplo.com',
    senha: 'editor123',
    roles: ['editor', 'usuario']
  },
  {
    id: 3,
    nome: 'Usuário',
    email: 'usuario@exemplo.com',
    senha: 'usuario123',
    roles: ['usuario']
  }
];

// Middleware de autenticação
function autenticar(req, res, next) {
  const authHeader = req.headers.authorization;
  
  if (!authHeader || !authHeader.startsWith('Bearer ')) {
    return res.status(401).json({ mensagem: 'Token não fornecido' });
  }
  
  const token = authHeader.split(' ')[1];
  
  try {
    const usuario = jwt.verify(token, JWT_SECRET);
    req.usuario = usuario;
    next();
  } catch (erro) {
    return res.status(403).json({ mensagem: 'Token inválido' });
  }
}

// Middleware de autorização baseada em função
function temRole(role) {
  return (req, res, next) => {
    if (!req.usuario || !req.usuario.roles) {
      return res.status(403).json({ mensagem: 'Acesso negado' });
    }
    
    if (req.usuario.roles.includes(role)) {
      return next();
    }
    
    res.status(403).json({ mensagem: 'Acesso negado - Permissão insuficiente' });
  };
}

// Rota de login
app.post('/login', (req, res) => {
  const { email, senha } = req.body;
  
  // Encontrar usuário
  const usuario = usuarios.find(u => u.email === email);
  
  if (!usuario || usuario.senha !== senha) {
    return res.status(401).json({ mensagem: 'Credenciais inválidas' });
  }
  
  // Criar payload do token
  const payload = {
    id: usuario.id,
    nome: usuario.nome,
    email: usuario.email,
    roles: usuario.roles
  };
  
  // Gerar token
  const token = jwt.sign(payload, JWT_SECRET, { expiresIn: '1h' });
  
  res.json({ token });
});

// Rota acessível para todos os usuários autenticados
app.get('/perfil', autenticar, (req, res) => {
  res.json({ usuario: req.usuario });
});

// Rota acessível apenas para editores
app.post('/artigos', autenticar, temRole('editor'), (req, res) => {
  res.json({ mensagem: 'Artigo criado com sucesso', dados: req.body });
});

// Rota acessível apenas para administradores
app.get('/usuarios', autenticar, temRole('admin'), (req, res) => {
  // Retorna lista de usuários (sem senhas)
  const listaUsuarios = usuarios.map(u => ({
    id: u.id,
    nome: u.nome,
    email: u.email,
    roles: u.roles
  }));
  
  res.json(listaUsuarios);
});

app.listen(3000, () => {
  console.log('Servidor rodando na porta 3000');
});
```

## Autenticação OAuth 2.0

OAuth 2.0 é um protocolo de autorização que permite que aplicativos obtenham acesso limitado a contas de usuários em serviços como Google, Facebook, GitHub, etc.

### Instalação
```bash
npm install passport passport-google-oauth20
```

### Implementação com Google
```javascript
const express = require('express');
const passport = require('passport');
const GoogleStrategy = require('passport-google-oauth20').Strategy;
const session = require('express-session');
const app = express();

// Configurações do Express
app.use(express.json());
app.use(session({
  secret: 'chave-secreta',
  resave: false,
  saveUninitialized: false
}));

// Inicialização do Passport
app.use(passport.initialize());
app.use(passport.session());

// Configuração da estratégia Google OAuth
passport.use(new GoogleStrategy({
  clientID: 'SEU_CLIENT_ID',
  clientSecret: 'SEU_CLIENT_SECRET',
  callbackURL: 'http://localhost:3000/auth/google/callback'
}, (accessToken, refreshToken, profile, done) => {
  // Aqui você normalmente salvaria ou buscaria o usuário no banco de dados
  // Para este exemplo, usaremos apenas o perfil retornado
  return done(null, profile);
}));

// Serialização e deserialização de usuários
passport.serializeUser((user, done) => {
  done(null, user);
});

passport.deserializeUser((obj, done) => {
  done(null, obj);
});

// Rota para iniciar a autenticação Google
app.get('/auth/google',
  passport.authenticate('google', { scope: ['profile', 'email'] })
);

// Callback após autenticação Google
app.get('/auth/google/callback',
  passport.authenticate('google', { failureRedirect: '/login' }),
  (req, res) => {
    // Autenticação bem-sucedida, redirecionar
    res.redirect('/perfil');
  }
);

// Middleware para verificar autenticação
function estaAutenticado(req, res, next) {
  if (req.isAuthenticated()) {
    return next();
  }
  res.redirect('/login');
}

// Rota protegida
app.get('/perfil', estaAutenticado, (req, res) => {
  res.json({ usuario: req.user });
});

// Rota de logout
app.get('/logout', (req, res) => {
  req.logout(function(err) {
    if (err) return next(err);
    res.redirect('/');
  });
});

app.listen(3000, () => {
  console.log('Servidor rodando na porta 3000');
});
```

## Boas Práticas de Segurança

### Armazenamento Seguro de Senhas
Nunca armazene senhas em texto simples! Use algoritmos de hash como bcrypt.

```javascript
const bcrypt = require('bcrypt');

// Hasheando a senha
async function hashSenha(senha) {
  const salt = await bcrypt.genSalt(10);
  return await bcrypt.hash(senha, salt);
}

// Verificando a senha
async function verificarSenha(senha, hash) {
  return await bcrypt.compare(senha, hash);
}

// Exemplo de uso
async function registrarUsuario(req, res) {
  const { nome, email, senha } = req.body;
  
  try {
    // Hash da senha antes de armazenar
    const senhaHash = await hashSenha(senha);
    
    // Criar usuário no banco de dados
    const novoUsuario = await Usuario.create({
      nome,
      email,
      senha: senhaHash
    });
    
    res.status(201).json({ mensagem: 'Usuário registrado com sucesso' });
  } catch (erro) {
    res.status(500).json({ mensagem: 'Erro ao registrar usuário' });
  }
}
```

### Rate Limiting
Limitar o número de tentativas de login para prevenir ataques de força bruta.

```javascript
const rateLimit = require('express-rate-limit');

// Limitar tentativas de login
const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutos
  max: 5, // 5 tentativas
  message: { mensagem: 'Muitas tentativas de login. Tente novamente mais tarde.' }
});

app.post('/login', loginLimiter, (req, res) => {
  // Lógica de login
});
```

### Segredos e Chaves
Nunca armazene chaves secretas ou credenciais no código-fonte. Use variáveis de ambiente.

```javascript
// .env
JWT_SECRET=minha-chave-secreta-muito-segura-e-longa
GOOGLE_CLIENT_ID=12345678
GOOGLE_CLIENT_SECRET=abcdefg

// No código
require('dotenv').config();
const JWT_SECRET = process.env.JWT_SECRET;
```

### Validação de Entrada
Sempre valide e sanitize todas as entradas do usuário.

```javascript
const { body, validationResult } = require('express-validator');

app.post('/registrar',
  [
    body('nome').trim().isLength({ min: 3 }).withMessage('Nome deve ter pelo menos 3 caracteres'),
    body('email').isEmail().normalizeEmail().withMessage('Email inválido'),
    body('senha').isLength({ min: 8 }).withMessage('Senha deve ter pelo menos 8 caracteres')
  ],
  (req, res) => {
    const erros = validationResult(req);
    
    if (!erros.isEmpty()) {
      return res.status(400).json({ erros: erros.array() });
    }
    
    // Prosseguir com o registro
  }
);
```

### Proteção CSRF
Para aplicações que usam autenticação baseada em sessão e cookies, implemente proteção CSRF.

```javascript
const csrf = require('csurf');
const cookieParser = require('cookie-parser');

app.use(cookieParser());
app.use(csrf({ cookie: true }));

// Middleware para adicionar token CSRF às respostas
app.use((req, res, next) => {
  res.locals.csrfToken = req.csrfToken();
  next();
});

// No lado do cliente, inclua o token CSRF nas requisições:
// Header: 'X-CSRF-Token': 'token-do-csrf'
```

### HTTPS
Sempre use HTTPS em produção para proteger a transmissão de dados sensíveis.

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=footer"/>