<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=header"/>

# Rotas e Métodos HTTP

## O que são Rotas HTTP?

Rotas HTTP são os caminhos (endpoints) que definem como os recursos podem ser acessados em uma aplicação web ou API. Uma rota é composta por um método HTTP e um caminho, como `GET /api/usuarios` ou `POST /api/produtos`.

## Métodos HTTP

Os métodos HTTP definem a ação a ser realizada sobre um recurso. Cada método tem uma semântica específica:

### GET
Usado para **recuperar** dados de um recurso sem modificá-lo.

```http
GET /api/produtos/123
```

Características:
- Somente leitura (idempotente)
- Não altera o estado do servidor
- Pode ser cacheado
- Parâmetros são enviados na URL

### POST
Usado para **criar** um novo recurso.

```http
POST /api/produtos
Content-Type: application/json

{
  "nome": "Smartphone",
  "preco": 1299.99,
  "descricao": "Smartphone de última geração"
}
```

Características:
- Altera o estado do servidor
- Não é idempotente (cada requisição pode criar um novo recurso)
- Dados enviados no corpo da requisição
- Normalmente retorna o recurso criado com status 201

### PUT
Usado para **atualizar completamente** um recurso existente.

```http
PUT /api/produtos/123
Content-Type: application/json

{
  "nome": "Smartphone Pro",
  "preco": 1499.99,
  "descricao": "Smartphone de última geração com câmera avançada"
}
```

Características:
- Substitui completamente o recurso existente
- É idempotente (múltiplas requisições têm o mesmo efeito)
- Requer todos os dados do recurso
- URL identifica o recurso específico a ser atualizado

### PATCH
Usado para **atualizar parcialmente** um recurso existente.

```http
PATCH /api/produtos/123
Content-Type: application/json

{
  "preco": 1399.99
}
```

Características:
- Atualiza apenas os campos especificados
- Requer apenas os dados que precisam ser atualizados
- Útil para atualizações parciais ou incrementais

### DELETE
Usado para **remover** um recurso.

```http
DELETE /api/produtos/123
```

Características:
- Remove o recurso especificado
- É idempotente (o resultado é o mesmo para múltiplas requisições)
- Normalmente retorna status 204 (No Content) após sucesso

### Outros Métodos
- **HEAD**: Similar ao GET, mas retorna apenas cabeçalhos, sem corpo
- **OPTIONS**: Retorna os métodos HTTP suportados pelo recurso
- **TRACE**: Usado para diagnóstico, retorna a requisição como foi recebida pelo servidor
- **CONNECT**: Estabelece um túnel para o servidor

## Estrutura de Rotas em Express

No Express, as rotas podem ser organizadas de várias formas:

### Método Básico

```javascript
const express = require('express');
const app = express();

// Rota básica com método GET
app.get('/usuario', (req, res) => {
  res.json({ nome: 'João', idade: 30 });
});

// Rota com método POST
app.post('/usuario', (req, res) => {
  // Criando um novo usuário
  res.status(201).json({ mensagem: 'Usuário criado com sucesso' });
});
```

### Múltiplos Métodos para uma Rota

```javascript
app.route('/usuario')
  .get((req, res) => {
    // Obter usuário
    res.json({ nome: 'João', idade: 30 });
  })
  .post((req, res) => {
    // Criar usuário
    res.status(201).json({ mensagem: 'Usuário criado' });
  })
  .put((req, res) => {
    // Atualizar usuário
    res.json({ mensagem: 'Usuário atualizado' });
  })
  .delete((req, res) => {
    // Remover usuário
    res.status(204).end();
  });
```

### Router do Express

```javascript
// arquivo: routes/usuarios.js
const express = require('express');
const router = express.Router();

// Definindo rotas no router
router.get('/', (req, res) => {
  res.json([{ nome: 'João' }, { nome: 'Maria' }]);
});

router.get('/:id', (req, res) => {
  res.json({ id: req.params.id, nome: 'João' });
});

router.post('/', (req, res) => {
  res.status(201).json({ mensagem: 'Usuário criado' });
});

module.exports = router;

// arquivo: app.js
const express = require('express');
const app = express();
const usuariosRouter = require('./routes/usuarios');

// Usando o router com um prefixo
app.use('/usuarios', usuariosRouter);
```

## Parâmetros e Consultas

### Parâmetros de Rota

Utilizados para identificar recursos específicos:

```javascript
// Parâmetro de rota (:id)
app.get('/produtos/:id', (req, res) => {
  const produtoId = req.params.id;
  res.json({ id: produtoId, nome: 'Produto Exemplo' });
});

// Múltiplos parâmetros
app.get('/categorias/:categoriaId/produtos/:produtoId', (req, res) => {
  const { categoriaId, produtoId } = req.params;
  res.json({ 
    categoria: categoriaId,
    produto: produtoId
  });
});
```

### Parâmetros de Consulta (Query String)

Utilizados para filtrar, ordenar ou paginar resultados:

```javascript
// /produtos?categoria=eletronicos&ordenar=preco&direcao=asc
app.get('/produtos', (req, res) => {
  const { categoria, ordenar, direcao } = req.query;
  
  // Simulando uma lista filtrada e ordenada
  let produtos = [
    { id: 1, nome: 'Smartphone', categoria: 'eletronicos', preco: 1299 },
    { id: 2, nome: 'Notebook', categoria: 'eletronicos', preco: 4500 },
    { id: 3, nome: 'Camiseta', categoria: 'vestuario', preco: 49 }
  ];
  
  // Filtrando por categoria
  if (categoria) {
    produtos = produtos.filter(p => p.categoria === categoria);
  }
  
  // Ordenando
  if (ordenar === 'preco') {
    produtos.sort((a, b) => {
      return direcao === 'asc' ? a.preco - b.preco : b.preco - a.preco;
    });
  }
  
  res.json(produtos);
});
```

## Corpo da Requisição

Para receber dados enviados no corpo da requisição, é necessário usar middlewares:

```javascript
const express = require('express');
const app = express();

// Middleware para processar JSON
app.use(express.json());

// Middleware para processar dados de formulários
app.use(express.urlencoded({ extended: true }));

// Rota que recebe dados no corpo
app.post('/produtos', (req, res) => {
  const produto = req.body;
  
  // Validação básica
  if (!produto.nome || !produto.preco) {
    return res.status(400).json({ erro: 'Nome e preço são obrigatórios' });
  }
  
  // Processamento do produto
  console.log('Produto recebido:', produto);
  res.status(201).json({ 
    id: 123, 
    ...produto,
    mensagem: 'Produto criado com sucesso' 
  });
});
```

## Respostas HTTP

### Formatos de Resposta

```javascript
// Resposta JSON
app.get('/api/dados', (req, res) => {
  res.json({ mensagem: 'Dados em formato JSON' });
});

// Resposta HTML
app.get('/pagina', (req, res) => {
  res.send('<h1>Título da Página</h1><p>Conteúdo HTML</p>');
});

// Resposta de arquivo
app.get('/download', (req, res) => {
  res.download('./arquivos/documento.pdf', 'relatorio.pdf');
});

// Redirecionamento
app.get('/redirecionar', (req, res) => {
  res.redirect('/nova-pagina');
});

// Renderizando uma view (com template engine como EJS)
app.get('/perfil', (req, res) => {
  res.render('perfil', { usuario: { nome: 'João', idade: 30 } });
});
```

### Cabeçalhos Personalizados

```javascript
app.get('/api/dados', (req, res) => {
  // Definindo cabeçalhos personalizados
  res.setHeader('X-API-Version', '1.0');
  res.setHeader('Cache-Control', 'max-age=3600');
  
  // Enviando a resposta
  res.json({ dados: 'Exemplo' });
});
```

## Middleware para Rotas

Os middlewares são funções que têm acesso ao objeto de requisição, ao objeto de resposta e à próxima função no ciclo de requisição-resposta:

```javascript
// Middleware de autenticação
const verificarAutenticacao = (req, res, next) => {
  const token = req.headers.authorization;
  
  if (!token) {
    return res.status(401).json({ erro: 'Token não fornecido' });
  }
  
  // Validação do token (simplificada)
  if (token !== 'token-valido') {
    return res.status(401).json({ erro: 'Token inválido' });
  }
  
  // Usuário autenticado, continua para a próxima função
  next();
};

// Aplicando middleware a uma rota específica
app.get('/api/perfil', verificarAutenticacao, (req, res) => {
  res.json({ nome: 'Usuário Autenticado', nivel: 'admin' });
});

// Aplicando middleware a um grupo de rotas
const rotasProtegidas = express.Router();
rotasProtegidas.use(verificarAutenticacao);

rotasProtegidas.get('/financeiro', (req, res) => {
  res.json({ saldo: 1000 });
});

rotasProtegidas.get('/relatorios', (req, res) => {
  res.json({ relatorios: ['Janeiro', 'Fevereiro'] });
});

app.use('/admin', rotasProtegidas);
```

## Melhores Práticas para Rotas

### ✅ Recomendações:

- **Use substantivos, não verbos** para rotas (ex: `/produtos` em vez de `/getProdutos`)
- **Versione sua API** nos caminhos (ex: `/api/v1/produtos`)
- **Implemente paginação** para coleções grandes de dados
- **Organize rotas** em arquivos separados por domínio
- **Respeite a semântica HTTP** dos métodos
- **Documente suas rotas** com ferramentas como Swagger
- **Use códigos de status HTTP** apropriados

### ❌ Práticas a evitar:

- **Rotas muito longas** e complicadas
- **Misturar responsabilidades** em uma única rota
- **⚠️ Ignorar boas práticas** de nomenclatura de recursos
- **Usar apenas POST** para todas as operações
- **⚠️ Expor detalhes sensíveis** nos nomes das rotas ou respostas

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=footer"/>