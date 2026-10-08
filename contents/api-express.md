<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=header"/>

# Criando uma API com Express

## O que é Express.js?
Express.js é um framework web rápido, flexível e minimalista para Node.js que fornece um conjunto robusto de recursos para aplicações web e mobile. É o framework mais popular para Node.js e é usado para criar APIs RESTful e aplicações web.

### Características do Express.js:
- **Performance**: Framework leve com alta performance
- **Minimalista**: Fornece apenas o necessário, sem opiniões fortes
- **Extensível**: Facilmente extensível com middlewares
- **Ecossistema rico**: Grande quantidade de plugins e extensões disponíveis
- **Rotas flexíveis**: Sistema de roteamento poderoso e intuitivo

## Iniciando um projeto Express

### Configuração inicial
```bash
# Criar uma nova pasta para o projeto
mkdir minha-api-express
cd minha-api-express

# Inicializar o package.json
npm init -y

# Instalar o Express
npm install express
```

### Criando um servidor básico
```javascript
// Arquivo: app.js
const express = require('express');
const app = express();
const PORT = process.env.PORT || 3000;

// Configurando middleware para processar JSON
app.use(express.json());

// Rota inicial
app.get('/', (req, res) => {
  res.json({ mensagem: 'API funcionando!' });
});

// Iniciando o servidor
app.listen(PORT, () => {
  console.log(`Servidor rodando na porta ${PORT}`);
});
```

## Estrutura de projeto recomendada

```
minha-api-express/
  ├── controllers/     # Lógica das rotas
  ├── models/          # Modelos de dados
  ├── routes/          # Definições de rotas
  ├── middlewares/     # Funções de middleware
  ├── utils/           # Funções utilitárias
  ├── config/          # Configurações
  ├── app.js           # Ponto de entrada da aplicação
  ├── server.js        # Inicialização do servidor
  └── package.json     # Dependências
```

### Exemplo de estruturação do código

#### routes/usuarios.js
```javascript
const express = require('express');
const router = express.Router();
const usuariosController = require('../controllers/usuarios');

router.get('/', usuariosController.listarTodos);
router.get('/:id', usuariosController.buscarPorId);
router.post('/', usuariosController.criar);
router.put('/:id', usuariosController.atualizar);
router.delete('/:id', usuariosController.remover);

module.exports = router;
```

#### controllers/usuarios.js
```javascript
const usuarios = [
  { id: 1, nome: 'Ana Silva', email: 'ana@exemplo.com' },
  { id: 2, nome: 'João Costa', email: 'joao@exemplo.com' }
];

// Listar todos os usuários
exports.listarTodos = (req, res) => {
  res.json(usuarios);
};

// Buscar usuário por ID
exports.buscarPorId = (req, res) => {
  const id = parseInt(req.params.id);
  const usuario = usuarios.find(u => u.id === id);
  
  if (!usuario) {
    return res.status(404).json({ mensagem: 'Usuário não encontrado' });
  }
  
  res.json(usuario);
};

// Criar novo usuário
exports.criar = (req, res) => {
  const { nome, email } = req.body;
  
  if (!nome || !email) {
    return res.status(400).json({ mensagem: 'Nome e email são obrigatórios' });
  }
  
  const novoId = usuarios.length > 0 ? Math.max(...usuarios.map(u => u.id)) + 1 : 1;
  const novoUsuario = { id: novoId, nome, email };
  
  usuarios.push(novoUsuario);
  res.status(201).json(novoUsuario);
};

// Atualizar usuário existente
exports.atualizar = (req, res) => {
  const id = parseInt(req.params.id);
  const index = usuarios.findIndex(u => u.id === id);
  
  if (index === -1) {
    return res.status(404).json({ mensagem: 'Usuário não encontrado' });
  }
  
  const { nome, email } = req.body;
  
  if (!nome && !email) {
    return res.status(400).json({ mensagem: 'Nenhum dado fornecido para atualização' });
  }
  
  usuarios[index] = { 
    ...usuarios[index], 
    nome: nome || usuarios[index].nome,
    email: email || usuarios[index].email
  };
  
  res.json(usuarios[index]);
};

// Remover usuário
exports.remover = (req, res) => {
  const id = parseInt(req.params.id);
  const index = usuarios.findIndex(u => u.id === id);
  
  if (index === -1) {
    return res.status(404).json({ mensagem: 'Usuário não encontrado' });
  }
  
  const usuarioRemovido = usuarios.splice(index, 1)[0];
  res.json({ mensagem: 'Usuário removido com sucesso', usuario: usuarioRemovido });
};
```

#### app.js atualizado
```javascript
const express = require('express');
const app = express();
const usuariosRoutes = require('./routes/usuarios');

// Configurando middleware para processar JSON
app.use(express.json());

// Configurando rotas
app.use('/api/usuarios', usuariosRoutes);

app.get('/', (req, res) => {
  res.json({ mensagem: 'API funcionando!' });
});

module.exports = app;
```

#### server.js
```javascript
const app = require('./app');
const PORT = process.env.PORT || 3000;

app.listen(PORT, () => {
  console.log(`Servidor rodando na porta ${PORT}`);
});
```

## Middlewares em Express

Middlewares são funções que têm acesso ao objeto de requisição (req), objeto de resposta (res) e à próxima função middleware (next) no ciclo de requisição-resposta.

### Tipos de middleware:
- **Middleware de aplicação**: Usado em toda a aplicação
- **Middleware de rota**: Usado apenas em rotas específicas
- **Middleware de erro**: Usado para tratamento de erros
- **Middleware de terceiros**: Pacotes npm (como o cors, morgan, etc)

### Exemplos de middlewares

#### Middleware personalizado para logging
```javascript
// middlewares/logger.js
function logger(req, res, next) {
  console.log(`${new Date().toISOString()} - ${req.method} ${req.url}`);
  next(); // Passa o controle para o próximo middleware
}

module.exports = logger;
```

#### Middleware para autenticação
```javascript
// middlewares/auth.js
function autenticar(req, res, next) {
  const token = req.header('Authorization');
  
  if (!token || token !== 'token-secreto') {
    return res.status(401).json({ mensagem: 'Acesso não autorizado' });
  }
  
  next();
}

module.exports = autenticar;
```

#### Aplicando os middlewares
```javascript
const express = require('express');
const app = express();
const logger = require('./middlewares/logger');
const autenticar = require('./middlewares/auth');
const usuariosRoutes = require('./routes/usuarios');

// Middleware global
app.use(express.json());
app.use(logger);

// Middleware de rota
app.use('/api/usuarios', autenticar, usuariosRoutes);

app.get('/', (req, res) => {
  res.json({ mensagem: 'API funcionando!' });
});

module.exports = app;
```

## Ferramentas úteis para Express

### Nodemon: Desenvolvimento com Auto-Reload

#### Instalação e Configuração
```bash
# Instalação global
npm install -g nodemon

# Instalação local (recomendado)
npm install nodemon --save-dev
```

#### Configuração no package.json
```json
{
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js",
    "debug": "nodemon --inspect server.js"
  }
}
```

#### Configuração Avançada (nodemon.json)
```json
{
  "watch": [
    "src/",
    "config/"
  ],
  "ext": "js,json,yaml,env",
  "ignore": [
    "src/logs/*",
    "src/tests/*",
    "src/**/*.test.js"
  ],
  "env": {
    "NODE_ENV": "development",
    "PORT": "3000"
  },
  "execMap": {
    "js": "node --trace-warnings",
    "ts": "ts-node"
  },
  "delay": "1500"
}
```

#### Opções Comuns
- **watch**: Lista de diretórios para monitorar
- **ext**: Extensões de arquivo para monitorar
- **ignore**: Arquivos/diretórios para ignorar
- **delay**: Atraso antes de reiniciar (em ms)
- **exec**: Comando personalizado para execução
- **env**: Variáveis de ambiente

#### Exemplos de Uso

##### Executando com TypeScript
```bash
nodemon --exec ts-node ./src/server.ts
```

##### Com variáveis de ambiente
```bash
nodemon -r dotenv/config server.js
```

##### Com argumentos específicos
```bash
nodemon --watch src --ignore tests/ server.js
```

##### Comandos Úteis Durante Execução
- **rs**: Reinicia manualmente o servidor
- **CTRL + C**: Para o servidor
- **clear**: Limpa o console

##### Problemas Comuns e Soluções

###### Reinícios em Loop
```json
{
  "ignore": [
    ".git",
    "node_modules/**/node_modules",
    "*.test.js"
  ]
}
```

###### Arquivos Temporários
```json
{
  "ignore": [
    "*.tmp",
    "temp/*"
  ]
}
```

###### Problemas com Memória
```bash
nodemon --max-old-space-size=4096 server.js
```

###### Integração com Debugger
```json
{
  "scripts": {
    "debug": "nodemon --inspect server.js"
  }
}
```

#### Boas Práticas
1. **Instale localmente**: Evite instalações globais
2. **Use scripts npm**: Configure no package.json
3. **Configure ignore**: Evite monitorar arquivos desnecessários
4. **Defina extensões**: Especifique apenas as necessárias
5. **Use delay**: Em projetos grandes para evitar múltiplos reloads

### Validação de dados com express-validator
```bash
npm install express-validator
```

```javascript
const { body, validationResult } = require('express-validator');

// Middleware de validação
const validarUsuario = [
  body('nome').notEmpty().withMessage('Nome é obrigatório'),
  body('email').isEmail().withMessage('E-mail inválido'),
  (req, res, next) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ erros: errors.array() });
    }
    next();
  }
];

// Aplicando nas rotas
router.post('/', validarUsuario, usuariosController.criar);
```

### CORS para permitir acesso cross-origin
```bash
npm install cors
```

```javascript
const express = require('express');
const cors = require('cors');
const app = express();

// Habilitando CORS para todas as origens
app.use(cors());

// OU restringindo origens específicas
app.use(cors({
  origin: 'https://meuapp.com'
}));
```

## Integrando com banco de dados

### Exemplo com MongoDB e Mongoose
```bash
npm install mongoose
```

```javascript
// config/database.js
const mongoose = require('mongoose');

const conectarBD = async () => {
  try {
    await mongoose.connect('mongodb://localhost:27017/minha-api', {
      useNewUrlParser: true,
      useUnifiedTopology: true
    });
    console.log('Conectado ao MongoDB');
  } catch (erro) {
    console.error('Erro ao conectar ao MongoDB:', erro.message);
    process.exit(1);
  }
};

module.exports = conectarBD;
```

```javascript
// models/Usuario.js
const mongoose = require('mongoose');

const UsuarioSchema = new mongoose.Schema({
  nome: {
    type: String,
    required: true
  },
  email: {
    type: String,
    required: true,
    unique: true
  },
  dataCriacao: {
    type: Date,
    default: Date.now
  }
});

module.exports = mongoose.model('Usuario', UsuarioSchema);
```

```javascript
// Atualizando server.js
const app = require('./app');
const conectarBD = require('./config/database');
const PORT = process.env.PORT || 3000;

// Conectar ao banco de dados
conectarBD();

app.listen(PORT, () => {
  console.log(`Servidor rodando na porta ${PORT}`);
});
```

## Boas práticas para APIs RESTful com Express

1. **Use substantivos para recursos, não verbos**
   - Bom: `/api/usuarios`
   - Evite: `/api/getUsuarios`

2. **Versione sua API**
   - Exemplo: `/api/v1/usuarios`

3. **Use filtros, ordenação e paginação para coleções**
   - Exemplo: `/api/usuarios?limite=10&pagina=2&ordenar=nome`

4. **Retorne códigos de status HTTP apropriados**
   - 200 OK, 201 Created, 400 Bad Request, 404 Not Found, etc.

5. **Implemente tratamento global de erros**
   ```javascript
   // middlewares/errorHandler.js
   function errorHandler(err, req, res, next) {
     console.error(err.stack);
     res.status(500).json({
       erro: 'Erro interno no servidor',
       detalhes: process.env.NODE_ENV === 'development' ? err.message : undefined
     });
   }
  
   // No final de app.js
   app.use(errorHandler);
   ```

6. **Use HTTPS em produção**
7. **Documente sua API** (Swagger/OpenAPI)
8. **Implemente logging e monitoramento**

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=footer"/>