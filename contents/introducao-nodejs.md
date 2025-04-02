<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=header"/>

# 🚀 Introdução ao Node.js

## 🤔 O que é Node.js?
Node.js é uma plataforma de execução JavaScript no backend, baseada no motor V8 do Google Chrome. Ele permite executar JavaScript no lado do servidor, sendo utilizado para criar aplicações escaláveis, principalmente voltadas para web.

### ✨ Características do Node.js
- **🧵 Single-threaded**: Utiliza um único thread para lidar com múltiplas requisições.
- **⚡ Non-blocking I/O**: Opera de forma assíncrona, permitindo maior eficiência na execução de tarefas.
- **🔄 Event Loop**: Mecanismo que gerencia a execução de código assíncrono.
- **📚 Call Stack**: Pilha de chamadas que armazena as funções em execução.
- **📋 Pilha de Eventos**: Responsável por gerenciar eventos e callbacks de forma assíncrona.

## 🧩 Módulos importantes

### 📁 File System (fs)
Permite a manipulação de arquivos no sistema.
```javascript
const fs = require('fs');
fs.readFile('arquivo.txt', 'utf8', (err, data) => {
  if (err) throw err;
  console.log(data);
});
```

### 🌐 HTTP
Possibilita a criação de servidores web.
```javascript
const http = require('http');
const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Olá, Mundo!');
});
server.listen(3000);
```

### 🛣️ Path
Facilita o trabalho com caminhos de arquivos e diretórios.
```javascript
const path = require('path');
const filePath = path.join(__dirname, 'arquivos', 'texto.txt');
```

## 📊 Vantagens do Node.js

- **🚄 Alta performance**: Ideal para aplicações com muitas conexões simultâneas
- **📦 Ecossistema rico**: Milhares de pacotes disponíveis via npm
- **👨‍💻 Mesma linguagem**: Uso de JavaScript tanto no frontend quanto no backend
- **🔄 Atualizações constantes**: Comunidade ativa e crescente

## 🔍 Quando usar Node.js?

Node.js é excelente para:
- Aplicações em tempo real (chats, jogos)
- APIs e microsserviços
- Aplicações de streaming
- Ferramentas de linha de comando

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=footer"/> 