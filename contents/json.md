<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=header"/>

# 📑 Estrutura de Dados JSON

## 🤔 O que é JSON?
JSON (JavaScript Object Notation) é um formato leve de intercâmbio de dados, fácil para humanos lerem e escreverem, e simples para máquinas analisarem e gerarem. É baseado em um subconjunto da linguagem JavaScript, mas é independente de linguagem.

### 🔹 Características do JSON:
- **🧩 Formato de texto simples**: Independente de linguagem
- **🔄 Compatível com JavaScript**: Pode ser convertido diretamente em objetos JavaScript
- **🌐 Universalmente aceito**: Utilizado em várias linguagens e sistemas
- **📊 Auto-descritivo**: A estrutura de dados é clara
- **🔄 Facilmente serializável**: Ideal para transferir dados pela web

## 📋 Sintaxe básica do JSON

### 🧰 Tipos de dados suportados:
- **🔢 Números**: Inteiros ou decimais sem aspas
- **📝 Strings**: Texto entre aspas duplas
- **✅ Booleanos**: true ou false
- **🚫 null**: Representa valor nulo
- **📚 Arrays**: Coleção ordenada de valores
- **📦 Objetos**: Coleção de pares chave-valor

### 📊 Exemplos de estruturas JSON:

#### 📄 Objeto simples
```json
{
  "nome": "Maria Silva",
  "idade": 28,
  "email": "maria@exemplo.com",
  "ativo": true
}
```

#### 📚 Array de objetos
```json
[
  {
    "id": 1,
    "produto": "Notebook",
    "preco": 3500,
    "disponivel": true
  },
  {
    "id": 2,
    "produto": "Smartphone",
    "preco": 2000,
    "disponivel": false
  }
]
```

#### 🔄 Estruturas aninhadas
```json
{
  "empresa": "TechSolutions",
  "fundacao": 2010,
  "funcionarios": [
    {
      "id": 101,
      "nome": "João",
      "departamento": "Desenvolvimento",
      "habilidades": ["JavaScript", "Node.js", "React"]
    },
    {
      "id": 102,
      "nome": "Ana",
      "departamento": "Design",
      "habilidades": ["UI/UX", "Figma", "Adobe XD"]
    }
  ],
  "endereco": {
    "rua": "Avenida Central",
    "numero": 1000,
    "cidade": "São Paulo"
  }
}
```

## 🔄 Trabalhando com JSON em Node.js

### 📤 Convertendo objetos para JSON (Serialização)
```javascript
const usuario = {
  nome: "Pedro Souza",
  idade: 32,
  email: "pedro@exemplo.com",
  ativo: true
};

// Convertendo para string JSON
const usuarioJSON = JSON.stringify(usuario);
console.log(usuarioJSON);
// Saída: {"nome":"Pedro Souza","idade":32,"email":"pedro@exemplo.com","ativo":true}

// Com formatação para melhor legibilidade
const usuarioJSONFormatado = JSON.stringify(usuario, null, 2);
console.log(usuarioJSONFormatado);
/*
{
  "nome": "Pedro Souza",
  "idade": 32,
  "email": "pedro@exemplo.com",
  "ativo": true
}
*/
```

### 📥 Convertendo JSON para objetos (Desserialização)
```javascript
const jsonString = '{"produto":"Monitor","preco":1200,"estoque":15}';

// Convertendo para objeto JavaScript
const produto = JSON.parse(jsonString);
console.log(produto.produto); // Monitor
console.log(produto.preco);   // 1200
```

### 💾 Salvando e lendo JSON em arquivos
```javascript
const fs = require('fs');

// Salvando dados em um arquivo JSON
const dados = {
  titulo: "Aprendendo JSON",
  autor: "Desenvolvedores Node.js",
  topicos: ["Sintaxe", "Serialização", "Desserialização"],
  versao: 1.0
};

fs.writeFileSync('dados.json', JSON.stringify(dados, null, 2));

// Lendo dados de um arquivo JSON
const dadosLidos = JSON.parse(fs.readFileSync('dados.json', 'utf8'));
console.log(dadosLidos.titulo);  // Aprendendo JSON
console.log(dadosLidos.topicos); // ["Sintaxe", "Serialização", "Desserialização"]
```

## 🛡️ Boas práticas ao trabalhar com JSON

1. **🔍 Validação**: Sempre valide o JSON antes de processá-lo
   ```javascript
   try {
     const dados = JSON.parse(jsonString);
     // Processar dados...
   } catch (erro) {
     console.error("JSON inválido:", erro.message);
   }
   ```

2. **🔐 Segurança**: Cuidado com JSON de fontes não confiáveis (possível injeção)
3. **📏 Tamanho**: Evite documentos JSON muito grandes
4. **📄 Documentação**: Documente a estrutura esperada para facilitar o uso
5. **📚 Bibliotecas específicas**: Use bibliotecas como `ajv` para validação de schema JSON

### 🧪 Validação com schemas JSON
```javascript
const Ajv = require('ajv');
const ajv = new Ajv();

const schema = {
  type: "object",
  properties: {
    nome: { type: "string" },
    idade: { type: "number", minimum: 0 },
    email: { type: "string", format: "email" }
  },
  required: ["nome", "email"]
};

const dados = {
  nome: "Carlos",
  idade: 25,
  email: "carlos@exemplo.com"
};

const valido = ajv.validate(schema, dados);
if (valido) {
  console.log("Dados válidos!");
} else {
  console.log("Erro de validação:", ajv.errors);
}
```

## 📌 JSON vs. XML
JSON frequentemente substitui XML por ser mais leve e fácil de processar:

### 👍 Vantagens do JSON sobre XML:
- **📉 Sintaxe mais concisa**: Menos verboso
- **⚡ Processamento mais rápido**: Em geral, parsing mais eficiente
- **🧩 Integração natural com JavaScript**: Conversão direta para objetos
- **📊 Facilidade de leitura**: Estrutura mais simples e intuitiva

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=footer"/> 