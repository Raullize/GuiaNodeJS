<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=header"/>

# 🔢 Códigos de Status HTTP

## 🔍 O que são Códigos de Status HTTP?
Os códigos de status HTTP são números padronizados retornados pelos servidores web para informar o resultado de uma requisição. Eles são fundamentais para o desenvolvimento de APIs, pois comunicam claramente ao cliente se a requisição foi bem-sucedida ou se ocorreu algum erro.

## 🗂️ Categorias de Códigos

Os códigos de status são agrupados em cinco categorias, cada uma representada pelo primeiro dígito:

| Categoria | Significado | Exemplo |
|-----------|-------------|---------|
| **1xx** | Informativo | Requisição recebida, processamento continua |
| **2xx** | Sucesso | Requisição bem-sucedida |
| **3xx** | Redirecionamento | Ação adicional necessária |
| **4xx** | Erro do cliente | Problema na requisição |
| **5xx** | Erro do servidor | Problema no servidor |

## 📋 Códigos mais comuns e suas aplicações

### ℹ️ 1xx - Informativos
Estes códigos indicam que a requisição foi recebida e o processo continua.

- **100 Continue**: O servidor recebeu os cabeçalhos da requisição e o cliente deve continuar enviando o corpo da requisição
- **101 Switching Protocols**: O servidor está trocando de protocolo conforme solicitado pelo cliente

### ✅ 2xx - Sucesso
Indica que a requisição foi recebida, compreendida e aceita com sucesso.

- **200 OK**: A requisição foi bem-sucedida (usado em GET, PUT, PATCH)
- **201 Created**: A requisição foi bem-sucedida e um novo recurso foi criado (usado em POST)
- **202 Accepted**: A requisição foi aceita para processamento, mas o processamento não foi concluído
- **204 No Content**: A requisição foi bem-sucedida, mas não há conteúdo para retornar (usado em DELETE)
- **206 Partial Content**: Entrega parcial do recurso solicitado

### 🔄 3xx - Redirecionamento
Indica que ações adicionais precisam ser tomadas para completar a requisição.

- **301 Moved Permanently**: O recurso foi movido permanentemente para outra URL
- **302 Found**: O recurso foi temporariamente movido para outra URL
- **304 Not Modified**: O recurso não foi modificado desde a última requisição (usado com cache)
- **307 Temporary Redirect**: Redirecionamento temporário preservando o método HTTP
- **308 Permanent Redirect**: Redirecionamento permanente preservando o método HTTP

### ❌ 4xx - Erros do Cliente
Indica que houve um erro na requisição feita pelo cliente.

- **400 Bad Request**: A requisição contém sintaxe inválida ou não pode ser processada
- **401 Unauthorized**: A requisição requer autenticação do usuário
- **403 Forbidden**: O servidor entendeu a requisição, mas se recusa a autorizá-la
- **404 Not Found**: O recurso solicitado não foi encontrado
- **405 Method Not Allowed**: O método HTTP especificado não é permitido para o recurso
- **409 Conflict**: Conflito no estado atual do recurso (ex: tentativa de criar recurso existente)
- **413 Payload Too Large**: A requisição é maior do que o servidor está disposto a processar
- **422 Unprocessable Entity**: A requisição está bem formada, mas contém erros semânticos
- **429 Too Many Requests**: O cliente enviou muitas requisições em um determinado período

### 🔧 5xx - Erros do Servidor
Indica que o servidor falhou ao processar uma requisição aparentemente válida.

- **500 Internal Server Error**: Erro genérico no servidor
- **501 Not Implemented**: O servidor não suporta a funcionalidade solicitada
- **502 Bad Gateway**: O servidor, enquanto atuando como gateway, recebeu uma resposta inválida
- **503 Service Unavailable**: O servidor não está pronto para lidar com a requisição (sobrecarga ou manutenção)
- **504 Gateway Timeout**: O servidor, enquanto atuando como gateway, não recebeu resposta a tempo

## 💻 Exemplos em Node.js com Express

```javascript
// Resposta de sucesso
app.get('/api/usuarios', (req, res) => {
  const usuarios = [{ nome: 'João' }, { nome: 'Maria' }];
  res.status(200).json(usuarios);
});

// Criação de recurso
app.post('/api/usuarios', (req, res) => {
  // Lógica para criar usuário
  res.status(201).json({ id: 3, nome: req.body.nome });
});

// Recurso não encontrado
app.get('/api/usuarios/:id', (req, res) => {
  const usuario = buscarUsuario(req.params.id);
  if (!usuario) {
    return res.status(404).json({ mensagem: 'Usuário não encontrado' });
  }
  res.status(200).json(usuario);
});

// Erro de validação
app.post('/api/produtos', (req, res) => {
  if (!req.body.nome || !req.body.preco) {
    return res.status(400).json({ 
      mensagem: 'Nome e preço são obrigatórios',
      erros: {
        nome: !req.body.nome ? 'Campo obrigatório' : null,
        preco: !req.body.preco ? 'Campo obrigatório' : null
      }
    });
  }
  // Continua processamento
});

// Erro interno do servidor
app.get('/api/relatorios', (req, res) => {
  try {
    // Código que pode gerar erro
    const relatorio = gerarRelatorio();
    res.status(200).json(relatorio);
  } catch (erro) {
    console.error('Erro ao gerar relatório:', erro);
    res.status(500).json({ 
      mensagem: 'Erro interno ao processar sua solicitação'
    });
  }
});
```

## 📊 Como usar adequadamente os códigos de status

### ✅ Para APIs bem projetadas:
- **Use códigos específicos** em vez de apenas 200 ou 500
- **Seja consistente** no uso dos códigos em toda a API
- **Inclua mensagens descritivas** junto com os códigos
- **Documente os códigos** que sua API utiliza
- **Use o código 202** para operações assíncronas longas
- **Diferencie 401 (não autenticado) e 403 (não autorizado)**

### ❌ Erros comuns a evitar:
- Retornar 200 OK com mensagem de erro no corpo
- Usar 404 para erros de autorização
- Expor detalhes técnicos de erros 500 em produção
- Ignorar os códigos 429 (rate limiting) e 405 (método não permitido)

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=footer"/> 