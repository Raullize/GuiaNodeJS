<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=header"/>

# Criando um Servidor com Node.js

## Introdução aos Servidores Web

Um servidor web é uma aplicação que processa requisições HTTP e retorna respostas aos clientes. Com Node.js, podemos criar servidores web eficientes e de alta performance utilizando o módulo HTTP nativo ou frameworks como Express.

## Módulo HTTP Nativo

O Node.js possui um módulo HTTP integrado que permite criar servidores web sem dependências externas.

### Servidor Básico com HTTP

```javascript
// Importando o módulo HTTP
const http = require('http');

// Criando o servidor
const server = http.createServer((req, res) => {
  // Configurando cabeçalhos de resposta
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  
  // Enviando a resposta
  res.end('Olá, Mundo! Bem-vindo ao meu servidor Node.js\n');
});

// Definindo a porta
const PORT = 3000;

// Iniciando o servidor
server.listen(PORT, () => {
  console.log(`Servidor rodando em http://localhost:${PORT}/`);
});
```

### Manipulando Diferentes Rotas

```javascript
const http = require('http');
const url = require('url');

const server = http.createServer((req, res) => {
  // Obtendo a URL e o caminho da requisição
  const parsedUrl = url.parse(req.url, true);
  const path = parsedUrl.pathname;
  const trimmedPath = path.replace(/^\/+|\/+$/g, '');
  
  // Configurando o cabeçalho de resposta
  res.setHeader('Content-Type', 'text/plain');
  
  // Roteamento básico
  switch(trimmedPath) {
    case '':
      res.statusCode = 200;
      res.end('Página inicial\n');
      break;
    case 'sobre':
      res.statusCode = 200;
      res.end('Sobre nós\n');
      break;
    case 'contato':
      res.statusCode = 200;
      res.end('Entre em contato\n');
      break;
    default:
      res.statusCode = 404;
      res.end('Página não encontrada\n');
  }
});

server.listen(3000, () => {
  console.log('Servidor rodando na porta 3000');
});
```

### Métodos HTTP

Podemos processar diferentes métodos HTTP (GET, POST, PUT, DELETE) para criar um servidor mais completo:

```javascript
const http = require('http');
const url = require('url');

const server = http.createServer((req, res) => {
  // Obtendo detalhes da requisição
  const parsedUrl = url.parse(req.url, true);
  const path = parsedUrl.pathname;
  const method = req.method.toLowerCase();
  
  res.setHeader('Content-Type', 'application/json');
  
  // Simulando banco de dados
  const usuarios = [
    { id: 1, nome: 'João' },
    { id: 2, nome: 'Maria' }
  ];
  
  // Rota /api/usuarios
  if (path === '/api/usuarios') {
    if (method === 'get') {
      res.statusCode = 200;
      res.end(JSON.stringify(usuarios));
    } else if (method === 'post') {
      let body = '';
      
      // Coletando dados enviados pelo cliente
      req.on('data', (chunk) => {
        body += chunk.toString();
      });
      
      // Processando os dados coletados
      req.on('end', () => {
        try {
          const novoUsuario = JSON.parse(body);
          novoUsuario.id = usuarios.length + 1;
          usuarios.push(novoUsuario);
          
          res.statusCode = 201;
          res.end(JSON.stringify(novoUsuario));
        } catch (e) {
          res.statusCode = 400;
          res.end(JSON.stringify({ erro: 'Formato JSON inválido' }));
        }
      });
    } else {
      res.statusCode = 405;
      res.end(JSON.stringify({ erro: 'Método não permitido' }));
    }
  } else {
    res.statusCode = 404;
    res.end(JSON.stringify({ erro: 'Rota não encontrada' }));
  }
});

server.listen(3000, () => {
  console.log('Servidor API rodando na porta 3000');
});
```

## Criando Servidor com Express

O Express é um framework web minimalista que facilita a criação de servidores e APIs.

### Instalação do Express

```bash
npm install express
```

### Servidor Básico com Express

```javascript
// Importando o Express
const express = require('express');

// Criando a aplicação Express
const app = express();

// Definindo uma rota
app.get('/', (req, res) => {
  res.send('Olá, Mundo! Bem-vindo ao servidor Express');
});

// Definindo a porta
const PORT = 3000;

// Iniciando o servidor
app.listen(PORT, () => {
  console.log(`Servidor Express rodando em http://localhost:${PORT}/`);
});
```

### Rotas com Express

O Express torna o roteamento muito mais simples:

```javascript
const express = require('express');
const app = express();

// Middleware para processar dados JSON
app.use(express.json());

// Rotas
app.get('/', (req, res) => {
  res.send('Página inicial');
});

app.get('/sobre', (req, res) => {
  res.send('Sobre nós');
});

app.get('/contato', (req, res) => {
  res.send('Entre em contato');
});

// Rota com parâmetro
app.get('/usuarios/:id', (req, res) => {
  res.send(`Exibindo usuário ${req.params.id}`);
});

// Rota com query params
app.get('/produtos', (req, res) => {
  const categoria = req.query.categoria;
  if (categoria) {
    res.send(`Produtos da categoria: ${categoria}`);
  } else {
    res.send('Todos os produtos');
  }
});

// Middleware para rotas não encontradas
app.use((req, res) => {
  res.status(404).send('Página não encontrada');
});

app.listen(3000, () => {
  console.log('Servidor Express rodando na porta 3000');
});
```

### API RESTful com Express

O Express facilita a criação de APIs RESTful:

```javascript
const express = require('express');
const app = express();

// Middleware para processar JSON
app.use(express.json());

// Simulando banco de dados
let usuarios = [
  { id: 1, nome: 'João', email: 'joao@email.com' },
  { id: 2, nome: 'Maria', email: 'maria@email.com' }
];

// Rotas da API
// GET - Listar todos os usuários
app.get('/api/usuarios', (req, res) => {
  res.json(usuarios);
});

// GET - Obter um usuário específico
app.get('/api/usuarios/:id', (req, res) => {
  const id = parseInt(req.params.id);
  const usuario = usuarios.find(u => u.id === id);
  
  if (!usuario) {
    return res.status(404).json({ mensagem: 'Usuário não encontrado' });
  }
  
  res.json(usuario);
});

// POST - Criar um novo usuário
app.post('/api/usuarios', (req, res) => {
  if (!req.body.nome || !req.body.email) {
    return res.status(400).json({ mensagem: 'Nome e email são obrigatórios' });
  }
  
  const novoUsuario = {
    id: usuarios.length + 1,
    nome: req.body.nome,
    email: req.body.email
  };
  
  usuarios.push(novoUsuario);
  res.status(201).json(novoUsuario);
});

// PUT - Atualizar um usuário
app.put('/api/usuarios/:id', (req, res) => {
  const id = parseInt(req.params.id);
  const index = usuarios.findIndex(u => u.id === id);
  
  if (index === -1) {
    return res.status(404).json({ mensagem: 'Usuário não encontrado' });
  }
  
  if (!req.body.nome || !req.body.email) {
    return res.status(400).json({ mensagem: 'Nome e email são obrigatórios' });
  }
  
  const usuarioAtualizado = {
    id: id,
    nome: req.body.nome,
    email: req.body.email
  };
  
  usuarios[index] = usuarioAtualizado;
  res.json(usuarioAtualizado);
});

// DELETE - Remover um usuário
app.delete('/api/usuarios/:id', (req, res) => {
  const id = parseInt(req.params.id);
  const index = usuarios.findIndex(u => u.id === id);
  
  if (index === -1) {
    return res.status(404).json({ mensagem: 'Usuário não encontrado' });
  }
  
  const usuarioRemovido = usuarios[index];
  usuarios = usuarios.filter(u => u.id !== id);
  
  res.json(usuarioRemovido);
});

app.listen(3000, () => {
  console.log('API Express rodando na porta 3000');
});
```

## WebSockets com Node.js

Para comunicação em tempo real, podemos usar WebSockets com a biblioteca Socket.IO:

```javascript
// Instalação: npm install express socket.io

const express = require('express');
const http = require('http');
const socketIo = require('socket.io');

const app = express();
const server = http.createServer(app);
const io = socketIo(server);

// Servindo um HTML básico
app.get('/', (req, res) => {
  res.sendFile(__dirname + '/index.html');
});

// Lógica do Socket.IO
io.on('connection', (socket) => {
  console.log('Um usuário conectou');
  
  // Enviando mensagem para o cliente
  socket.emit('bemVindo', { mensagem: 'Bem-vindo ao chat!' });
  
  // Recebendo mensagens do cliente
  socket.on('chatMensagem', (msg) => {
    console.log('Mensagem recebida: ' + msg);
    
    // Transmitindo mensagem para todos os clientes
    io.emit('chatMensagem', msg);
  });
  
  // Quando o cliente desconecta
  socket.on('disconnect', () => {
    console.log('Usuário desconectou');
  });
});

server.listen(3000, () => {
  console.log('Servidor WebSocket rodando na porta 3000');
});
```

HTML do cliente:
```html
<!DOCTYPE html>
<html>
<head>
  <title>Chat com Socket.IO</title>
</head>
<body>
  <h1>Chat em Tempo Real</h1>
  <div id="mensagens"></div>
  <input id="mensagem" placeholder="Digite sua mensagem" />
  <button onclick="enviarMensagem()">Enviar</button>

  <script src="/socket.io/socket.io.js"></script>
  <script>
    const socket = io();
    
    // Recebendo mensagem de boas-vindas
    socket.on('bemVindo', (data) => {
      adicionarMensagem('Sistema: ' + data.mensagem);
    });
    
    // Recebendo mensagens do chat
    socket.on('chatMensagem', (msg) => {
      adicionarMensagem(msg);
    });
    
    function enviarMensagem() {
      const input = document.getElementById('mensagem');
      const mensagem = input.value;
      
      if (mensagem) {
        socket.emit('chatMensagem', mensagem);
        input.value = '';
      }
    }
    
    function adicionarMensagem(msg) {
      const mensagens = document.getElementById('mensagens');
      const elemento = document.createElement('div');
      elemento.textContent = msg;
      mensagens.appendChild(elemento);
    }
  </script>
</body>
</html>
```

## Melhores Práticas

### ✅ Recomendações:
- **Utilize HTTPS** para comunicações seguras
- **Implemente middleware** para tratamento de erro e logging
- **Estruture bem suas rotas** para organização e manutenibilidade
- **Use nodemon** durante o desenvolvimento para reiniciar automaticamente o servidor
- **Monitore o desempenho** de seu servidor em produção
- **Utilize sistema de cache** para melhorar a performance

### ❌ Práticas a evitar:
- **Código bloqueante** que possa travar o Event Loop
- **Expor informações sensíveis** nos logs ou respostas de erro
- **⚠️ Ignorar tratamento de exceções** que podem derrubar o servidor
- **Usar operações síncronas** em rotas de alta demanda

## Trabalhando com __dirname e __filename

### O que são?
- **__dirname**: Variável global que contém o caminho absoluto do diretório do arquivo atual
- **__filename**: Variável global que contém o caminho absoluto completo do arquivo atual

### Exemplos de uso

```javascript
const path = require('path');

// Imprimindo os valores
console.log('__dirname:', __dirname);
console.log('__filename:', __filename);

// Construindo caminhos de arquivos
const configPath = path.join(__dirname, 'config', 'database.js');
const uploadsDir = path.join(__dirname, '..', 'uploads');
const publicDir = path.join(__dirname, 'public');

// Servindo arquivos estáticos com Express
app.use(express.static(path.join(__dirname, 'public')));

// Lendo um arquivo de configuração
const config = require(path.join(__dirname, 'config.json'));

// Criando diretórios
const fs = require('fs');
const logsDir = path.join(__dirname, 'logs');
if (!fs.existsSync(logsDir)) {
    fs.mkdirSync(logsDir);
}

// Resolvendo caminhos relativos
const relativePath = path.relative(__dirname, '/var/www/html');
console.log('Caminho relativo:', relativePath);

// Usando em rotas para servir arquivos
app.get('/download', (req, res) => {
    const file = path.join(__dirname, 'files', 'documento.pdf');
    res.download(file);
});

// Em configurações de template engine
app.set('views', path.join(__dirname, 'views'));
```

### Boas Práticas
1. **Sempre use path.join()**: Para garantir compatibilidade entre sistemas operacionais
2. **Evite concatenação de strings**: Não use `__dirname + '/arquivo'`
3. **Considere caminhos relativos**: Use `path.relative()` quando necessário
4. **Organize arquivos estáticos**: Use uma estrutura clara de diretórios
5. **Verifique existência**: Sempre verifique se arquivos/diretórios existem antes de usá-los

### Práticas a Evitar
```javascript
// ❌ Não faça isso
const badPath = __dirname + '/config/database.js';
const wrongPath = __dirname + '\\config\\database.js';

// ✅ Faça isso
const goodPath = path.join(__dirname, 'config', 'database.js');
```

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=footer"/>