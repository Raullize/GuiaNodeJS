<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=header"/>

# APIs REST

## O que é uma API REST?
Uma API REST (Representational State Transfer) é um padrão arquitetural que define como os sistemas podem se comunicar de forma padronizada via HTTP. Este modelo utiliza os princípios e protocolos da web para facilitar a integração entre diferentes aplicações.

## Princípios Fundamentais
- **Recursos**: Tudo é tratado como um recurso, identificado por URIs
- **Representações**: Recursos podem ter múltiplas representações (JSON, XML)
- **Stateless**: Cada requisição contém todas as informações necessárias
- **Interface Uniforme**: Uso consistente de HTTP e seus métodos
- **Arquitetura em Camadas**: Componentes separados e independentes

## Como funciona?
1. O cliente faz uma **requisição HTTP** para um endpoint da API
2. O servidor processa a requisição e acessa os dados necessários
3. O servidor responde com os dados estruturados (geralmente em **JSON**)
4. O frontend recebe e processa a resposta

## Métodos HTTP e suas aplicações

| Método | Função | Exemplo de uso |
|--------|--------|----------------|
| **GET** | Recuperar dados | Obter uma lista de usuários |
| **POST** | Criar um recurso | Adicionar um novo usuário |
| **PUT** | Atualizar um recurso (completo) | Atualizar todos os dados de um usuário |
| **PATCH** | Atualizar parcialmente | Atualizar apenas o email do usuário |
| **DELETE** | Remover um recurso | Excluir um usuário |

## Exemplos de Endpoints

```
# Usuários
GET    /api/usuarios       # Lista todos os usuários
GET    /api/usuarios/123   # Obtém detalhes do usuário 123
POST   /api/usuarios       # Cria um novo usuário
PUT    /api/usuarios/123   # Atualiza completamente o usuário 123
PATCH  /api/usuarios/123   # Atualiza parcialmente o usuário 123
DELETE /api/usuarios/123   # Remove o usuário 123

# Produtos
GET    /api/produtos?categoria=eletronicos   # Lista produtos com filtro
GET    /api/produtos?ordenar=preco&direcao=asc   # Lista ordenada
```

## Exemplos de Requisições

### GET (Obter todos os usuários)
```http
GET http://minhaapi.com/api/usuarios
Accept: application/json
```

### GET (Obter um usuário específico)
```http
GET http://minhaapi.com/api/usuarios/1
Accept: application/json
```

### ➕ POST (Criar um novo usuário)
```http
POST http://minhaapi.com/api/usuarios
Content-Type: application/json

{
    "nome": "Ana Silva",
    "email": "ana.silva@email.com",
    "telefone": "+5511987654321"
}
```

### PUT (Atualizar um usuário)
```http
PUT http://minhaapi.com/api/usuarios/1
Content-Type: application/json

{
    "nome": "Ana Paula Silva",
    "email": "ana.silva@email.com",
    "telefone": "+5511987654321"
}
```

## Vantagens das APIs REST

- **Multiplataforma**: Pode ser consumida por clientes web, mobile e desktop
- **Escalabilidade**: Separação entre cliente e servidor facilita escalabilidade
- **Segurança**: Suporta diferentes mecanismos de autenticação e autorização
- **Formato JSON**: Leve e amplamente compatível para troca de dados
- **Cache**: Melhora a performance com recursos de cache HTTP

## Implementação em Node.js com Express

```javascript
const express = require('express');
const app = express();

// Middleware para processar JSON
app.use(express.json());

// Simulando um banco de dados
const usuarios = [
  { id: 1, nome: 'João Silva', email: 'joao@email.com' },
  { id: 2, nome: 'Maria Santos', email: 'maria@email.com' }
];

// GET - Listar todos os usuários
app.get('/api/usuarios', (req, res) => {
  res.json(usuarios);
});

// GET - Obter usuário específico
app.get('/api/usuarios/:id', (req, res) => {
  const usuario = usuarios.find(u => u.id === parseInt(req.params.id));
  if (!usuario) return res.status(404).json({ mensagem: 'Usuário não encontrado' });
  res.json(usuario);
});

// POST - Criar novo usuário
app.post('/api/usuarios', (req, res) => {
  const novoUsuario = {
    id: usuarios.length + 1,
    nome: req.body.nome,
    email: req.body.email
  };
  usuarios.push(novoUsuario);
  res.status(201).json(novoUsuario);
});

// Iniciando o servidor
app.listen(3000, () => console.log('Servidor rodando na porta 3000'));
```

## Boas Práticas

- **Documente sua API**: Use ferramentas como Swagger/OpenAPI
- **Versione sua API**: Ex: `/api/v1/usuarios`
- **Implemente testes**: Garanta que mudanças não quebrem funcionalidades
- **Use limitação de taxa**: Proteja contra abusos com rate limiting
- **Implemente paginação**: Para grandes conjuntos de dados
- **Utilize HTTPS**: Sempre proteja suas comunicações

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=footer"/>