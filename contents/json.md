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
const addFormats = require('ajv-formats');
const ajv = new Ajv();
addFormats(ajv);

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

## 🔧 Validação e Manipulação Avançada

### 🎯 Schemas JSON complexos
```javascript
const Ajv = require('ajv');
const addFormats = require('ajv-formats');
const ajv = new Ajv({ allErrors: true });
addFormats(ajv);

// Schema complexo para um sistema de e-commerce
const produtoSchema = {
  type: "object",
  properties: {
    id: { type: "string", pattern: "^[A-Z]{2}[0-9]{6}$" },
    nome: { type: "string", minLength: 3, maxLength: 100 },
    preco: { type: "number", minimum: 0.01, multipleOf: 0.01 },
    categoria: { 
      type: "string", 
      enum: ["eletronicos", "roupas", "casa", "livros"] 
    },
    tags: {
      type: "array",
      items: { type: "string" },
      minItems: 1,
      maxItems: 10,
      uniqueItems: true
    },
    especificacoes: {
      type: "object",
      properties: {
        peso: { type: "number", minimum: 0 },
        dimensoes: {
          type: "object",
          properties: {
            altura: { type: "number", minimum: 0 },
            largura: { type: "number", minimum: 0 },
            profundidade: { type: "number", minimum: 0 }
          },
          required: ["altura", "largura", "profundidade"]
        }
      }
    },
    dataLancamento: { type: "string", format: "date" },
    disponivel: { type: "boolean" }
  },
  required: ["id", "nome", "preco", "categoria", "disponivel"],
  additionalProperties: false
};

// Função de validação com tratamento de erros detalhado
function validarProduto(produto) {
  const valido = ajv.validate(produtoSchema, produto);
  
  if (!valido) {
    const erros = ajv.errors.map(erro => ({
      campo: erro.instancePath || erro.schemaPath,
      valor: erro.data,
      mensagem: erro.message,
      valorEsperado: erro.schema
    }));
    
    return {
      valido: false,
      erros: erros
    };
  }
  
  return { valido: true, erros: [] };
}

// Exemplo de uso
const produto = {
  id: "EL123456",
  nome: "Smartphone XYZ",
  preco: 899.99,
  categoria: "eletronicos",
  tags: ["smartphone", "android", "5g"],
  especificacoes: {
    peso: 180,
    dimensoes: {
      altura: 15.5,
      largura: 7.2,
      profundidade: 0.8
    }
  },
  dataLancamento: "2024-01-15",
  disponivel: true
};

const resultado = validarProduto(produto);
console.log(resultado);
```

### 🔄 Transformação e sanitização de dados
```javascript
// Função para sanitizar e transformar dados JSON
function sanitizarDados(dados) {
  return {
    ...dados,
    nome: dados.nome?.trim().toLowerCase(),
    email: dados.email?.trim().toLowerCase(),
    telefone: dados.telefone?.replace(/\D/g, ''), // Remove caracteres não numéricos
    dataCriacao: new Date().toISOString(),
    // Remove campos sensíveis
    senha: undefined,
    token: undefined
  };
}

// Validação condicional baseada em contexto
function criarValidadorCondicional(tipoUsuario) {
  const baseSchema = {
    type: "object",
    properties: {
      nome: { type: "string", minLength: 2 },
      email: { type: "string", format: "email" }
    },
    required: ["nome", "email"]
  };
  
  if (tipoUsuario === 'admin') {
    baseSchema.properties.permissoes = {
      type: "array",
      items: { type: "string" },
      minItems: 1
    };
    baseSchema.required.push('permissoes');
  }
  
  if (tipoUsuario === 'empresa') {
    baseSchema.properties.cnpj = {
      type: "string",
      pattern: "^\\d{2}\\.\\d{3}\\.\\d{3}/\\d{4}-\\d{2}$"
    };
    baseSchema.required.push('cnpj');
  }
  
  return ajv.compile(baseSchema);
}
```

## ⚡ Performance com Grandes Volumes de JSON

### 📊 Estratégias para otimização de performance

#### 🔄 Streaming de JSON para arquivos grandes
```javascript
const fs = require('fs');
const { Transform } = require('stream');
const StreamValues = require('stream-json/streamers/StreamValues');
const parser = require('stream-json');

// Processamento de JSON em stream para arquivos grandes (>100MB)
function processarJSONGrande(caminhoArquivo) {
  return new Promise((resolve, reject) => {
    let contador = 0;
    const resultados = [];
    
    const pipeline = fs.createReadStream(caminhoArquivo)
      .pipe(parser())
      .pipe(StreamValues.withParser())
      .pipe(new Transform({
        objectMode: true,
        transform(chunk, encoding, callback) {
          const dados = chunk.value;
          
          // Processa cada item individualmente
          if (dados.ativo && dados.preco > 100) {
            resultados.push({
              id: dados.id,
              nome: dados.nome,
              preco: dados.preco
            });
          }
          
          contador++;
          
          // Log de progresso a cada 10000 itens
          if (contador % 10000 === 0) {
            console.log(`Processados ${contador} itens...`);
          }
          
          callback();
        }
      }));
    
    pipeline.on('finish', () => {
      console.log(`Processamento concluído. Total: ${contador} itens`);
      resolve(resultados);
    });
    
    pipeline.on('error', reject);
  });
}
```

#### 🚀 Otimizações de parsing e serialização
```javascript
// Comparação de performance entre diferentes métodos
const { performance } = require('perf_hooks');

// Dados de teste
const dadosGrandes = Array.from({ length: 100000 }, (_, i) => ({
  id: i,
  nome: `Usuario ${i}`,
  email: `usuario${i}@exemplo.com`,
  dados: {
    preferencias: Array.from({ length: 10 }, (_, j) => `pref${j}`),
    historico: Array.from({ length: 50 }, (_, k) => ({ acao: `acao${k}`, timestamp: Date.now() }))
  }
}));

// Teste de serialização
function testarSerializacao() {
  console.log('🧪 Testando performance de serialização...');
  
  // Método padrão
  const inicio1 = performance.now();
  const json1 = JSON.stringify(dadosGrandes);
  const fim1 = performance.now();
  console.log(`JSON.stringify: ${(fim1 - inicio1).toFixed(2)}ms`);
  
  // Com replacer para otimizar
  const inicio2 = performance.now();
  const json2 = JSON.stringify(dadosGrandes, ['id', 'nome', 'email']); // Apenas campos necessários
  const fim2 = performance.now();
  console.log(`JSON.stringify (otimizado): ${(fim2 - inicio2).toFixed(2)}ms`);
  
  console.log(`Tamanho original: ${json1.length} caracteres`);
  console.log(`Tamanho otimizado: ${json2.length} caracteres`);
  console.log(`Redução: ${((1 - json2.length / json1.length) * 100).toFixed(1)}%`);
}

// Teste de parsing
function testarParsing() {
  console.log('\n🧪 Testando performance de parsing...');
  
  const jsonString = JSON.stringify(dadosGrandes.slice(0, 10000)); // Amostra menor
  
  // Parsing padrão
  const inicio1 = performance.now();
  const dados1 = JSON.parse(jsonString);
  const fim1 = performance.now();
  console.log(`JSON.parse: ${(fim1 - inicio1).toFixed(2)}ms`);
  
  // Parsing com reviver para transformação
  const inicio2 = performance.now();
  const dados2 = JSON.parse(jsonString, (key, value) => {
    // Converte timestamps em objetos Date
    if (key === 'timestamp' && typeof value === 'number') {
      return new Date(value);
    }
    return value;
  });
  const fim2 = performance.now();
  console.log(`JSON.parse (com reviver): ${(fim2 - inicio2).toFixed(2)}ms`);
}

// Executar testes
testarSerializacao();
testarParsing();
```

#### 💾 Compressão e cache para JSON
```javascript
const zlib = require('zlib');
const crypto = require('crypto');

// Sistema de cache com compressão
class JSONCache {
  constructor() {
    this.cache = new Map();
    this.maxSize = 100; // Máximo de itens no cache
  }
  
  // Gera hash para usar como chave
  gerarChave(dados) {
    return crypto.createHash('md5')
      .update(JSON.stringify(dados))
      .digest('hex');
  }
  
  // Comprime JSON antes de armazenar
  async armazenar(chave, dados) {
    try {
      const jsonString = JSON.stringify(dados);
      const comprimido = await new Promise((resolve, reject) => {
        zlib.gzip(jsonString, (err, result) => {
          if (err) reject(err);
          else resolve(result);
        });
      });
      
      // Remove item mais antigo se cache estiver cheio
      if (this.cache.size >= this.maxSize) {
        const primeiraChave = this.cache.keys().next().value;
        this.cache.delete(primeiraChave);
      }
      
      this.cache.set(chave, {
        dados: comprimido,
        timestamp: Date.now(),
        tamanhoOriginal: jsonString.length,
        tamanhoComprimido: comprimido.length
      });
      
      console.log(`📦 Cache: ${jsonString.length} → ${comprimido.length} bytes (${((1 - comprimido.length / jsonString.length) * 100).toFixed(1)}% redução)`);
      
    } catch (erro) {
      console.error('Erro ao armazenar no cache:', erro);
    }
  }
  
  // Recupera e descomprime dados
  async recuperar(chave) {
    const item = this.cache.get(chave);
    if (!item) return null;
    
    try {
      const descomprimido = await new Promise((resolve, reject) => {
        zlib.gunzip(item.dados, (err, result) => {
          if (err) reject(err);
          else resolve(result.toString());
        });
      });
      
      return JSON.parse(descomprimido);
    } catch (erro) {
      console.error('Erro ao recuperar do cache:', erro);
      return null;
    }
  }
  
  // Estatísticas do cache
  estatisticas() {
    const itens = Array.from(this.cache.values());
    const tamanhoTotal = itens.reduce((acc, item) => acc + item.tamanhoComprimido, 0);
    const tamanhoOriginalTotal = itens.reduce((acc, item) => acc + item.tamanhoOriginal, 0);
    
    return {
      itens: this.cache.size,
      tamanhoTotal: tamanhoTotal,
      tamanhoOriginal: tamanhoOriginalTotal,
      reducaoMedia: ((1 - tamanhoTotal / tamanhoOriginalTotal) * 100).toFixed(1) + '%'
    };
  }
}

// Exemplo de uso do cache
async function exemploCache() {
  const cache = new JSONCache();
  
  const dadosExemplo = {
    usuarios: dadosGrandes.slice(0, 1000),
    metadata: {
      versao: '1.0',
      geradoEm: new Date().toISOString()
    }
  };
  
  const chave = cache.gerarChave(dadosExemplo);
  
  // Armazena no cache
  await cache.armazenar(chave, dadosExemplo);
  
  // Recupera do cache
  const dadosRecuperados = await cache.recuperar(chave);
  
  console.log('✅ Dados recuperados com sucesso:', dadosRecuperados !== null);
  console.log('📊 Estatísticas do cache:', cache.estatisticas());
}

// exemploCache();
```

### 🎯 Dicas de performance para JSON

1. **🔄 Use streaming**: Para arquivos > 50MB, sempre use streaming
2. **📦 Comprima dados**: Use gzip/deflate para reduzir tamanho
3. **🎯 Filtre campos**: Serialize apenas campos necessários
4. **💾 Implemente cache**: Cache resultados de parsing pesados
5. **⚡ Evite parsing desnecessário**: Valide antes de fazer parse
6. **🔧 Use bibliotecas otimizadas**: Como `fast-json-stringify` para serialização
7. **📊 Monitore performance**: Use `performance.now()` para medir tempos

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