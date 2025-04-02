<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=header"/>

# 📨 Requisições e Respostas HTTP

## 🔍 O que são requisições HTTP?
Requisições HTTP são mensagens enviadas por um cliente (como um navegador) para solicitar recursos ou ações de um servidor web. Elas seguem um formato padronizado que inclui método, URI, cabeçalhos e corpo.

### 📋 Estrutura de uma requisição HTTP:
- **📝 Linha de requisição**: Contém o método HTTP, o caminho do recurso e a versão do protocolo
- **📑 Cabeçalhos**: Informações adicionais sobre a requisição
- **📊 Corpo**: Dados enviados ao servidor (opcional, dependendo do método)

## 📤 Métodos HTTP comuns
- **🔍 GET**: Solicita a recuperação de um recurso
- **📝 POST**: Envia dados para criar um recurso
- **🔄 PUT**: Atualiza um recurso existente
- **✏️ PATCH**: Atualiza parcialmente um recurso
- **🗑️ DELETE**: Remove um recurso

## 📥 Respostas HTTP
Respostas HTTP são mensagens enviadas pelo servidor em resposta a uma requisição do cliente. Elas contêm um código de status, cabeçalhos e, frequentemente, um corpo com os dados solicitados.

### 📋 Estrutura de uma resposta HTTP:
- **🔢 Linha de status**: Contém a versão do protocolo, código de status e mensagem
- **📑 Cabeçalhos**: Metadados sobre a resposta
- **📊 Corpo**: Conteúdo da resposta (quando aplicável)

## 🔄 Requisições e respostas em Node.js
No Node.js, podemos trabalhar com requisições e respostas HTTP usando módulos nativos ou frameworks como Express.

### 📊 Exemplo com HTTP nativo
```javascript
const http = require('http');

const server = http.createServer((req, res) => {
  // Informações da requisição
  console.log(`Método: ${req.method}`);
  console.log(`URL: ${req.url}`);
  console.log(`Headers: ${JSON.stringify(req.headers)}`);
  
  let body = '';
  
  // Lendo o corpo da requisição
  req.on('data', (chunk) => {
    body += chunk.toString();
  });
  
  req.on('end', () => {
    // Configurando a resposta
    res.statusCode = 200;
    res.setHeader('Content-Type', 'application/json');
    
    // Enviando a resposta
    const responseBody = { 
      message: 'Requisição recebida com sucesso',
      method: req.method,
      url: req.url,
      requestBody: body || 'Sem corpo'
    };
    
    res.end(JSON.stringify(responseBody));
  });
});

server.listen(3000, () => {
  console.log('Servidor rodando na porta 3000');
});
```

### 📱 Exemplo com Express.js
```javascript
const express = require('express');
const app = express();

// Middleware para processar JSON
app.use(express.json());

app.get('/api/usuarios', (req, res) => {
  // Exemplo de leitura de query params
  const { limite, pagina } = req.query;
  
  res.json({
    message: 'Lista de usuários',
    limite,
    pagina,
    dados: [{ id: 1, nome: 'João' }, { id: 2, nome: 'Maria' }]
  });
});

app.post('/api/usuarios', (req, res) => {
  // Lendo dados do corpo da requisição
  const novoUsuario = req.body;
  
  // Validação básica
  if (!novoUsuario.nome) {
    return res.status(400).json({ erro: 'Nome é obrigatório' });
  }
  
  // Simulando criação de usuário
  res.status(201).json({
    message: 'Usuário criado com sucesso',
    usuario: { id: 3, ...novoUsuario }
  });
});

app.listen(3000, () => {
  console.log('Servidor rodando na porta 3000');
});
```

## 📝 Cabeçalhos HTTP importantes
- **🔐 Authorization**: Usado para autenticação do cliente
- **📦 Content-Type**: Especifica o formato do corpo da requisição/resposta
- **📏 Content-Length**: Tamanho do corpo em bytes
- **🌐 Origin**: Identifica a origem da requisição (importante para CORS)
- **🔒 Cookie/Set-Cookie**: Gerenciamento de cookies
- **⚡ Cache-Control**: Diretivas para mecanismos de cache

## 🛠️ Ferramentas para testar requisições HTTP
- **🧪 Postman**: Interface gráfica para criação e teste de requisições
- **🖥️ cURL**: Ferramenta de linha de comando para transferência de dados
- **🧩 Insomnia**: Alternativa ao Postman, com interface limpa
- **🦊 Extensões de navegador**: Como REST Client para VS Code

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=footer"/> 