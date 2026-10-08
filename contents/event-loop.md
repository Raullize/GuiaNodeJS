<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=header"/>

# Event Loop, Call Stack e Non-blocking I/O

## Entendendo a execução assíncrona do Node.js

O Node.js é construído sobre um modelo de programação assíncrona e orientada a eventos, que é fundamental para sua alta performance e eficiência. Para entender como isso funciona, precisamos conhecer três conceitos essenciais: **Event Loop**, **Call Stack** e **Non-blocking I/O**.

## Call Stack (Pilha de Chamadas)

A Call Stack é uma estrutura de dados que armazena informações sobre as funções em execução no programa.

### Como funciona:
- **LIFO (Last In, First Out)**: A última função a entrar é a primeira a sair
- **⬆️ Push**: Quando uma função é chamada, ela é adicionada ao topo da pilha
- **⬇️ Pop**: Quando uma função termina, ela é removida da pilha
- **Execução síncrona**: Cada função na pilha é processada completamente antes de passar para a próxima

```javascript
// Exemplo de Call Stack
function primeiraFuncao() {
  console.log("Estou na primeira função");
  segundaFuncao();
  console.log("De volta à primeira função");
}

function segundaFuncao() {
  console.log("Estou na segunda função");
}

primeiraFuncao();

// Sequência na Call Stack:
// 1. primeiraFuncao() é adicionada à pilha
// 2. console.log("Estou na primeira função") é executado
// 3. segundaFuncao() é adicionada à pilha
// 4. console.log("Estou na segunda função") é executado
// 5. segundaFuncao() termina e é removida da pilha
// 6. console.log("De volta à primeira função") é executado
// 7. primeiraFuncao() termina e é removida da pilha
```

## Event Loop

O Event Loop é o coração do sistema assíncrono do Node.js, permitindo executar operações não-bloqueantes.

### Como funciona:
- **Monitoramento**: Verifica constantemente se a Call Stack está vazia
- **Fila de eventos**: Gerencia callbacks prontos para serem executados
- **Execução cíclica**: Transfere eventos da fila para a Call Stack quando esta está vazia
- **Prioridade**: Respeita a ordem de chegada dos eventos (com algumas exceções)

```javascript
// Exemplo de Event Loop
console.log("Início");

// Operação assíncrona (Timer)
setTimeout(() => {
  console.log("Callback do setTimeout");
}, 0);

// Operação assíncrona (I/O)
fs.readFile('arquivo.txt', () => {
  console.log("Arquivo lido");
});

// Promise (microtask)
Promise.resolve().then(() => {
  console.log("Promise resolvida");
});

console.log("Fim");

// Saída provável:
// Início
// Fim
// Promise resolvida (microtask tem prioridade)
// Callback do setTimeout
// Arquivo lido (tempo varia com o sistema de arquivos)
```

### Fases do Event Loop
O Event Loop do Node.js possui várias fases, cada uma com sua própria fila de callbacks:

1. **Timers**: Executa callbacks agendados por `setTimeout()` e `setInterval()`
2. **Pending callbacks**: Executa callbacks de operações I/O adiadas
3. **Idle, prepare**: Fase interna do Node.js
4. **Poll**: Busca novos eventos I/O e executa seus callbacks
5. **✅ Check**: Executa callbacks agendados por `setImmediate()`
6. **Close callbacks**: Executa callbacks de eventos de fechamento (ex: `socket.on('close', ...)`)

## Non-blocking I/O

O modelo de I/O não-bloqueante é fundamental para o desempenho do Node.js.

### Como funciona:
- **Delegação**: Operações de I/O são delegadas ao sistema operacional
- **Assincronicidade**: O programa continua executando enquanto espera resultados
- **Callbacks**: Quando a operação é concluída, o callback é enfileirado no Event Loop
- **Thread Pool**: Utiliza um pool de threads gerenciado pela libuv para operações pesadas

```javascript
// Exemplo de I/O não-bloqueante vs. bloqueante

// Não-bloqueante (assíncrono)
fs.readFile('arquivo.txt', (err, data) => {
  if (err) throw err;
  console.log("Dados lidos de forma assíncrona:", data);
});
console.log("Esta linha é executada imediatamente, sem esperar a leitura");

// Bloqueante (síncrono)
try {
  const data = fs.readFileSync('arquivo.txt');
  console.log("Dados lidos de forma síncrona:", data);
} catch (err) {
  console.error(err);
}
console.log("Esta linha só executa após a leitura do arquivo");
```

## Thread Pool

Apesar do Node.js ser single-threaded, algumas operações são processadas por um pool de threads:

- **Operações criptográficas**: funções do módulo `crypto`
- **Operações de arquivo**: filesystem I/O
- **DNS**: Resoluções de nomes de domínio
- **Operações intensivas de CPU**: Tarefas computacionais pesadas

```javascript
// Exemplo de operação que utiliza o thread pool
const crypto = require('crypto');

// Esta operação é computacionalmente intensiva e usa o thread pool
crypto.pbkdf2('senha', 'salt', 100000, 512, 'sha512', (err, derivedKey) => {
  if (err) throw err;
  console.log('Chave derivada:', derivedKey.toString('hex'));
});

console.log('Esta linha é executada enquanto a operação criptográfica ocorre em background');
```

## Microtasks e Macrotasks

O Event Loop do Node.js prioriza certos tipos de tarefas:

### Microtasks:
- **Promises** (`then`, `catch`, `finally`)
- **process.nextTick()** (máxima prioridade)
- **queueMicrotask()**

### Macrotasks:
- **setTimeout, setInterval**
- **✅ setImmediate**
- **I/O Operations**

```javascript
console.log('Script início');

setTimeout(() => {
  console.log('setTimeout 1');
}, 0);

Promise.resolve().then(() => {
  console.log('Promise 1');
}).then(() => {
  console.log('Promise 2');
});

process.nextTick(() => {
  console.log('nextTick');
});

setImmediate(() => {
  console.log('setImmediate');
});

console.log('Script fim');

// Saída:
// Script início
// Script fim
// nextTick (mais alta prioridade)
// Promise 1 (microtask)
// Promise 2 (microtask encadeada)
// setTimeout 1 (macrotask)
// setImmediate (macrotask, geralmente após o setTimeout)
```

## Melhores Práticas

### ✅ Recomendações:
- **Evite operações síncronas** bloqueantes em código de produção
- **Use Promises ou async/await** para melhor legibilidade de código assíncrono
- **⚠️ Tome cuidado com loops grandes** e operações intensivas de CPU
- **Divida tarefas intensivas** em partes menores usando `setImmediate`
- **Entenda as prioridades** de microtasks e macrotasks

### ❌ Práticas a evitar:
- **Operações bloqueantes** no event loop principal
- **Callbacks profundamente aninhados** (callback hell)
- **⚠️ Promessas não tratadas** (erros silenciosos)
- **Muito código síncrono** de execução longa

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=footer"/>