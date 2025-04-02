<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=header"/>

# ⚙️ Características do Node.js

## 🧠 Arquitetura Baseada em Eventos

### 🔄 Event Loop
O Event Loop é o coração do Node.js, responsável por gerenciar e processar eventos assíncronos. Ele:
- **🔍 Monitora** constantemente a fila de eventos
- **📥 Recebe** eventos e callbacks do Event Queue
- **📤 Delega** operações de I/O para o sistema operacional
- **⚡ Executa** callbacks quando operações são concluídas

```javascript
// Exemplo simplificado do Event Loop
console.log('Início');

setTimeout(() => {
  console.log('Executado após 2 segundos');
}, 2000);

console.log('Fim');

// Saída:
// Início
// Fim
// Executado após 2 segundos
```

## 👨‍💻 Programação Assíncrona

### ⚡ Non-Blocking I/O
Uma das principais características do Node.js é sua natureza não-bloqueante:

- **📈 Eficiência**: Permite lidar com várias operações simultaneamente
- **🔄 Continuidade**: O programa continua executando enquanto espera operações de I/O
- **💪 Escalabilidade**: Possibilita atender muitas requisições concorrentes

```javascript
// Exemplo de operação não-bloqueante
const fs = require('fs');

// Esta operação é executada em background
fs.readFile('arquivo.txt', 'utf8', (err, data) => {
  if (err) throw err;
  console.log('Conteúdo do arquivo:', data);
});

// O código continua executando imediatamente
console.log('Continuando execução enquanto o arquivo é lido...');
```

## 🧵 Single-Threaded

Node.js opera principalmente em uma única thread, mas consegue alta performance através de:

- **🔄 Event Loop**: Gerencia múltiplas operações assíncronas
- **🧰 Thread Pool**: Para operações pesadas (gerenciado pela libuv)
- **📡 Delegação**: Operações de I/O são delegadas ao sistema operacional

### 🌊 Thread Pool (libuv)
```javascript
// Operações que utilizam o thread pool
const crypto = require('crypto');

// Estas operações são pesadas e usam o thread pool
const hash1 = crypto.pbkdf2Sync('senha123', 'salt', 100000, 512, 'sha512');
const hash2 = crypto.pbkdf2Sync('senha456', 'salt', 100000, 512, 'sha512');

console.log('Operações de hash concluídas');
```

## 📦 Sistema de Módulos

O Node.js utiliza um sistema de módulos que permite organizar o código em partes reutilizáveis:

### 🏗️ CommonJS
```javascript
// Exportando módulos (module.exports)
// arquivo: matematica.js
function somar(a, b) {
  return a + b;
}

function subtrair(a, b) {
  return a - b;
}

module.exports = { somar, subtrair };

// Importando módulos (require)
// arquivo: app.js
const matematica = require('./matematica');
console.log(matematica.somar(5, 3)); // 8
```

### 🔄 ES Modules
```javascript
// Exportando (ESM)
// arquivo: matematica.mjs
export function somar(a, b) {
  return a + b;
}

// Importando (ESM)
// arquivo: app.mjs
import { somar } from './matematica.mjs';
console.log(somar(5, 3)); // 8
```

## 🔍 Garbage Collection

Node.js herda o sistema de gerenciamento de memória do V8:

- **🧹 Automático**: Libera memória de objetos não utilizados 
- **🔄 Incremental**: Minimiza pausas na execução do programa
- **🚀 Otimizado**: Algoritmos sofisticados de coleta de lixo

## 🌐 Escalabilidade

Node.js é altamente escalável devido a:

- **🔀 Arquitetura assíncrona**: Muitas conexões simultâneas
- **📡 Módulo Cluster**: Aproveita múltiplos núcleos de CPU
- **🔄 Balanceamento de carga**: Distribui requisições entre processos

```javascript
// Exemplo de uso do módulo Cluster
const cluster = require('cluster');
const http = require('http');
const numCPUs = require('os').cpus().length;

if (cluster.isMaster) {
  console.log(`Master ${process.pid} iniciado`);

  // Cria workers para cada núcleo da CPU
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }

  cluster.on('exit', (worker) => {
    console.log(`Worker ${worker.process.pid} encerrado`);
    cluster.fork(); // Substitui o worker que caiu
  });
} else {
  // Os workers compartilham a mesma porta
  http.createServer((req, res) => {
    res.writeHead(200);
    res.end('Hello World\n');
  }).listen(8000);

  console.log(`Worker ${process.pid} iniciado`);
}
```

## 📊 Comparação com Outras Plataformas

| Característica | Node.js | PHP | Python | Java |
|----------------|---------|-----|--------|------|
| Modelo de execução | Non-blocking | Blocking | Blocking | Blocking/Non-blocking |
| Threads | Single-threaded + Thread Pool | Thread por requisição | GIL (limitado) | Multi-threaded |
| Performance | Alta para I/O | Moderada | Moderada | Alta |
| Escalabilidade | Excelente | Boa | Boa | Excelente |
| Curva de aprendizado | Moderada | Baixa | Baixa | Alta |

## 💡 Quando Usar Node.js?

### ✅ Ideal para:
- **📱 APIs RESTful**: Resposta rápida, alta concorrência
- **🗣️ Aplicações em tempo real**: Chats, jogos, colaboração
- **📊 Dashboards**: Atualizações contínuas de dados
- **🔄 Streaming de dados**: Processamento de fluxos contínuos
- **🛠️ Ferramentas de linha de comando**: Scripts de automação

### ❌ Menos indicado para:
- **🧮 Processamento intensivo de CPU**: Cálculos complexos
- **🖥️ Aplicações CRUD simples**: Pode ser excessivo
- **⚙️ Operações bloqueantes**: Afeta todo o sistema

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=footer"/> 