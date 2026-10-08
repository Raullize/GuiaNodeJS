<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=header"/>

# Códigos de Status HTTP

## O que são Códigos de Status HTTP?
Os códigos de status HTTP são números padronizados retornados pelos servidores web para informar o resultado de uma requisição. Eles são fundamentais para o desenvolvimento de APIs, pois comunicam claramente ao cliente se a requisição foi bem-sucedida ou se ocorreu algum erro.

## Categorias de Códigos

Os códigos de status são agrupados em cinco categorias, cada uma representada pelo primeiro dígito:

| Categoria | Significado | Exemplo |
|-----------|-------------|---------|
| **1xx** | Informativo | Requisição recebida, processamento continua |
| **2xx** | Sucesso | Requisição bem-sucedida |
| **3xx** | Redirecionamento | Ação adicional necessária |
| **4xx** | Erro do cliente | Problema na requisição |
| **5xx** | Erro do servidor | Problema no servidor |

## Códigos mais comuns e suas aplicações

### 1xx - Informativos
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

### 3xx - Redirecionamento
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

### 5xx - Erros do Servidor
Indica que o servidor falhou ao processar uma requisição aparentemente válida.

- **500 Internal Server Error**: Erro genérico no servidor
- **501 Not Implemented**: O servidor não suporta a funcionalidade solicitada
- **502 Bad Gateway**: O servidor, enquanto atuando como gateway, recebeu uma resposta inválida
- **503 Service Unavailable**: O servidor não está pronto para lidar com a requisição (sobrecarga ou manutenção)
- **504 Gateway Timeout**: O servidor, enquanto atuando como gateway, não recebeu resposta a tempo

## Exemplos Práticos em Node.js com Express

### Cenários de Sucesso (2xx)

```javascript
// 200 OK - Busca bem-sucedida
app.get('/api/usuarios', (req, res) => {
  const usuarios = [{ nome: 'João' }, { nome: 'Maria' }];
  res.status(200).json({
    dados: usuarios,
    total: usuarios.length,
    pagina: 1
  });
});

// 201 Created - Criação de recurso
app.post('/api/usuarios', async (req, res) => {
  try {
    const novoUsuario = await criarUsuario(req.body);
    res.status(201).json({
      mensagem: 'Usuário criado com sucesso',
      usuario: novoUsuario,
      links: {
        self: `/api/usuarios/${novoUsuario.id}`,
        edit: `/api/usuarios/${novoUsuario.id}`,
        delete: `/api/usuarios/${novoUsuario.id}`
      }
    });
  } catch (erro) {
    res.status(400).json({ mensagem: erro.message });
  }
});

// 202 Accepted - Processamento assíncrono
app.post('/api/relatorios', (req, res) => {
  const jobId = gerarRelatorioAssincrono(req.body);
  res.status(202).json({
    mensagem: 'Relatório sendo processado',
    jobId: jobId,
    statusUrl: `/api/jobs/${jobId}/status`,
    estimativaMinutos: 5
  });
});

// 204 No Content - Deleção bem-sucedida
app.delete('/api/usuarios/:id', async (req, res) => {
  const usuarioExiste = await verificarUsuario(req.params.id);
  if (!usuarioExiste) {
    return res.status(404).json({ mensagem: 'Usuário não encontrado' });
  }
  
  await deletarUsuario(req.params.id);
  res.status(204).send(); // Sem conteúdo no corpo
});
```

### Cenários de Redirecionamento (3xx)

```javascript
// 301 Moved Permanently - API versionada
app.get('/api/v1/usuarios', (req, res) => {
  res.status(301).json({
    mensagem: 'Esta versão da API foi descontinuada',
    novaUrl: '/api/v2/usuarios',
    dataDescontinuacao: '2024-12-31'
  });
});

// 304 Not Modified - Cache condicional
app.get('/api/usuarios/:id', (req, res) => {
  const usuario = buscarUsuario(req.params.id);
  if (!usuario) {
    return res.status(404).json({ mensagem: 'Usuário não encontrado' });
  }
  
  const ifModifiedSince = req.headers['if-modified-since'];
  if (ifModifiedSince && new Date(usuario.ultimaModificacao) <= new Date(ifModifiedSince)) {
    return res.status(304).send();
  }
  
  res.status(200)
    .set('Last-Modified', usuario.ultimaModificacao)
    .json(usuario);
});
```

### ❌ Cenários de Erro do Cliente (4xx)

```javascript
// 400 Bad Request - Dados inválidos
app.post('/api/produtos', (req, res) => {
  const erros = [];
  
  if (!req.body.nome || req.body.nome.trim().length < 3) {
    erros.push({ campo: 'nome', mensagem: 'Nome deve ter pelo menos 3 caracteres' });
  }
  
  if (!req.body.preco || req.body.preco <= 0) {
    erros.push({ campo: 'preco', mensagem: 'Preço deve ser maior que zero' });
  }
  
  if (req.body.categoria && !['eletronicos', 'roupas', 'casa'].includes(req.body.categoria)) {
    erros.push({ campo: 'categoria', mensagem: 'Categoria inválida' });
  }
  
  if (erros.length > 0) {
    return res.status(400).json({
      mensagem: 'Dados de entrada inválidos',
      erros: erros,
      dadosRecebidos: req.body
    });
  }
  
  // Continua processamento...
});

// 401 Unauthorized - Não autenticado
app.get('/api/perfil', (req, res) => {
  const token = req.headers.authorization;
  
  if (!token) {
    return res.status(401).json({
      mensagem: 'Token de acesso requerido',
      erro: 'MISSING_TOKEN',
      comoResolver: 'Inclua o header Authorization: Bearer <seu-token>'
    });
  }
  
  if (!validarToken(token)) {
    return res.status(401).json({
      mensagem: 'Token inválido ou expirado',
      erro: 'INVALID_TOKEN',
      comoResolver: 'Faça login novamente para obter um novo token'
    });
  }
  
  // Continua...
});

// 403 Forbidden - Não autorizado
app.delete('/api/usuarios/:id', (req, res) => {
  const usuarioLogado = obterUsuarioDoToken(req.headers.authorization);
  const usuarioParaDeletar = req.params.id;
  
  // Usuário só pode deletar a própria conta, exceto admins
  if (usuarioLogado.id !== usuarioParaDeletar && usuarioLogado.role !== 'admin') {
    return res.status(403).json({
      mensagem: 'Acesso negado',
      erro: 'INSUFFICIENT_PERMISSIONS',
      detalhes: 'Você só pode deletar sua própria conta'
    });
  }
  
  // Continua...
});

// 404 Not Found - Recurso não encontrado
app.get('/api/usuarios/:id', (req, res) => {
  const usuario = buscarUsuario(req.params.id);
  
  if (!usuario) {
    return res.status(404).json({
      mensagem: 'Usuário não encontrado',
      erro: 'USER_NOT_FOUND',
      recursoSolicitado: `/api/usuarios/${req.params.id}`,
      sugestoes: [
        'Verifique se o ID está correto',
        'Consulte a lista de usuários em /api/usuarios'
      ]
    });
  }
  
  res.status(200).json(usuario);
});

// 405 Method Not Allowed - Método não permitido
app.all('/api/usuarios/:id', (req, res) => {
  const metodosPermitidos = ['GET', 'PUT', 'DELETE'];
  
  if (!metodosPermitidos.includes(req.method)) {
    return res.status(405)
      .set('Allow', metodosPermitidos.join(', '))
      .json({
        mensagem: `Método ${req.method} não permitido`,
        erro: 'METHOD_NOT_ALLOWED',
        metodosPermitidos: metodosPermitidos
      });
  }
  
  // Continua para outros middlewares...
});

// 409 Conflict - Conflito de estado
app.post('/api/usuarios', async (req, res) => {
  const emailExiste = await verificarEmailExistente(req.body.email);
  
  if (emailExiste) {
    return res.status(409).json({
      mensagem: 'Conflito: Email já está em uso',
      erro: 'EMAIL_ALREADY_EXISTS',
      campo: 'email',
      valor: req.body.email,
      comoResolver: 'Use um email diferente ou faça login se esta é sua conta'
    });
  }
  
  // Continua...
});

// 413 Payload Too Large - Arquivo muito grande
app.post('/api/upload', (req, res) => {
  const tamanhoMaximo = 5 * 1024 * 1024; // 5MB
  
  if (req.headers['content-length'] > tamanhoMaximo) {
    return res.status(413).json({
      mensagem: 'Arquivo muito grande',
      erro: 'FILE_TOO_LARGE',
      tamanhoRecebido: req.headers['content-length'],
      tamanhoMaximo: tamanhoMaximo,
      tamanhoMaximoFormatado: '5MB'
    });
  }
  
  // Continua...
});

// 422 Unprocessable Entity - Erro de validação semântica
app.post('/api/agendamentos', (req, res) => {
  const { dataInicio, dataFim, recursoId } = req.body;
  
  // Dados estão bem formados, mas há problemas semânticos
  if (new Date(dataInicio) >= new Date(dataFim)) {
    return res.status(422).json({
      mensagem: 'Erro de validação semântica',
      erro: 'INVALID_DATE_RANGE',
      detalhes: 'Data de início deve ser anterior à data de fim',
      campos: {
        dataInicio: dataInicio,
        dataFim: dataFim
      }
    });
  }
  
  if (verificarConflitosAgendamento(dataInicio, dataFim, recursoId)) {
    return res.status(422).json({
      mensagem: 'Conflito de agendamento',
      erro: 'SCHEDULE_CONFLICT',
      detalhes: 'Recurso já está agendado para este período'
    });
  }
  
  // Continua...
});

// 429 Too Many Requests - Rate limiting
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutos
  max: 100, // máximo 100 requisições por IP
  message: {
    mensagem: 'Muitas requisições',
    erro: 'RATE_LIMIT_EXCEEDED',
    limite: 100,
    janelaTempo: '15 minutos',
    tentarNovamenteEm: '15 minutos'
  },
  standardHeaders: true,
  legacyHeaders: false
});

app.use('/api/', limiter);
```

### Cenários de Erro do Servidor (5xx)

```javascript
// 500 Internal Server Error - Erro genérico
app.get('/api/relatorios', async (req, res) => {
  try {
    const relatorio = await gerarRelatorio();
    res.status(200).json(relatorio);
  } catch (erro) {
    console.error('Erro ao gerar relatório:', erro);
    
    // Em produção, não expor detalhes técnicos
    const isProducao = process.env.NODE_ENV === 'production';
    
    res.status(500).json({
      mensagem: 'Erro interno do servidor',
      erro: 'INTERNAL_SERVER_ERROR',
      timestamp: new Date().toISOString(),
      requestId: req.headers['x-request-id'],
      ...(isProducao ? {} : { detalhes: erro.message, stack: erro.stack })
    });
  }
});

// 502 Bad Gateway - Erro de serviço externo
app.get('/api/cotacao', async (req, res) => {
  try {
    const cotacao = await buscarCotacaoExterna();
    res.status(200).json(cotacao);
  } catch (erro) {
    if (erro.code === 'ECONNREFUSED' || erro.code === 'ETIMEDOUT') {
      return res.status(502).json({
        mensagem: 'Serviço de cotação temporariamente indisponível',
        erro: 'BAD_GATEWAY',
        servicoAfetado: 'API de Cotação Externa',
        tentarNovamenteEm: '5 minutos'
      });
    }
    
    res.status(500).json({ mensagem: 'Erro interno' });
  }
});

// 503 Service Unavailable - Manutenção
app.use((req, res, next) => {
  const emManutencao = process.env.MAINTENANCE_MODE === 'true';
  
  if (emManutencao) {
    return res.status(503).json({
      mensagem: 'Serviço temporariamente indisponível',
      erro: 'SERVICE_UNAVAILABLE',
      motivo: 'Manutenção programada',
      previsaoRetorno: '2024-01-15T10:00:00Z',
      contato: 'suporte@exemplo.com'
    });
  }
  
  next();
});

// 504 Gateway Timeout - Timeout de serviço externo
app.get('/api/dados-externos', async (req, res) => {
  const controller = new AbortController();
  const timeoutId = setTimeout(() => controller.abort(), 10000); // 10s timeout
  
  try {
    const dados = await fetch('https://api-externa.com/dados', {
      signal: controller.signal
    });
    
    clearTimeout(timeoutId);
    res.status(200).json(await dados.json());
  } catch (erro) {
    clearTimeout(timeoutId);
    
    if (erro.name === 'AbortError') {
      return res.status(504).json({
        mensagem: 'Timeout ao acessar serviço externo',
        erro: 'GATEWAY_TIMEOUT',
        timeout: '10 segundos',
        servicoAfetado: 'API Externa'
      });
    }
    
    res.status(500).json({ mensagem: 'Erro interno' });
  }
});
```

## Guia Completo de Uso dos Códigos de Status

### Quando usar cada código - Cenários Específicos

#### ✅ Códigos 2xx - Sucesso
- **200 OK**:
  - ✅ GET que retorna dados
  - ✅ PUT que atualiza recurso existente
  - ✅ PATCH que modifica parcialmente um recurso
- **201 Created**:
  - ✅ POST que cria novo recurso
  - ✅ Sempre incluir header `Location` com URL do novo recurso
- **202 Accepted**:
  - ✅ Operações assíncronas (envio de email, processamento de imagem)
  - ✅ Uploads grandes que serão processados em background
- **204 No Content**:
  - ✅ DELETE bem-sucedido
  - ✅ PUT que não retorna dados
  - ✅ Operações que não precisam retornar conteúdo

#### Códigos 3xx - Redirecionamento
- **301 Moved Permanently**:
  - ✅ Mudança definitiva de URL da API
  - ✅ Migração de versões antigas
- **302 Found**:
  - ✅ Redirecionamento temporário
  - ✅ Balanceamento de carga
- **304 Not Modified**:
  - ✅ Cache condicional com ETags
  - ✅ Otimização de bandwidth

#### ❌ Códigos 4xx - Erros do Cliente
- **400 Bad Request**:
  - ✅ JSON malformado
  - ✅ Parâmetros obrigatórios ausentes
  - ✅ Tipos de dados incorretos
- **401 Unauthorized**:
  - ✅ Token ausente ou inválido
  - ✅ Credenciais incorretas
  - ✅ Sessão expirada
- **403 Forbidden**:
  - ✅ Usuário autenticado mas sem permissão
  - ✅ Acesso a recurso restrito
  - ✅ Operação não permitida para o role do usuário
- **404 Not Found**:
  - ✅ Recurso não existe
  - ✅ Endpoint não implementado
  - ❌ NÃO usar para problemas de autorização
- **409 Conflict**:
  - ✅ Email já cadastrado
  - ✅ Violação de constraint única
  - ✅ Estado inconsistente do recurso
- **422 Unprocessable Entity**:
  - ✅ Dados válidos mas regras de negócio violadas
  - ✅ Relacionamentos inválidos
  - ✅ Validações semânticas
- **429 Too Many Requests**:
  - ✅ Rate limiting atingido
  - ✅ Proteção contra spam/DDoS

#### Códigos 5xx - Erros do Servidor
- **500 Internal Server Error**:
  - ✅ Exceções não tratadas
  - ✅ Erros de banco de dados
  - ✅ Falhas inesperadas
- **502 Bad Gateway**:
  - ✅ Serviço externo indisponível
  - ✅ Proxy/Gateway com problemas
- **503 Service Unavailable**:
  - ✅ Manutenção programada
  - ✅ Sobrecarga temporária
  - ✅ Recursos esgotados
- **504 Gateway Timeout**:
  - ✅ Timeout em serviços externos
  - ✅ Operações que demoram muito

### Middleware para Tratamento Centralizado de Erros

```javascript
// Middleware de tratamento de erros
app.use((err, req, res, next) => {
  // Log do erro
  console.error({
    timestamp: new Date().toISOString(),
    method: req.method,
    url: req.url,
    error: err.message,
    stack: err.stack,
    userAgent: req.headers['user-agent'],
    ip: req.ip
  });

  // Determinar código de status baseado no tipo de erro
  let statusCode = 500;
  let errorType = 'INTERNAL_SERVER_ERROR';
  let message = 'Erro interno do servidor';

  if (err.name === 'ValidationError') {
    statusCode = 400;
    errorType = 'VALIDATION_ERROR';
    message = 'Dados de entrada inválidos';
  } else if (err.name === 'UnauthorizedError') {
    statusCode = 401;
    errorType = 'UNAUTHORIZED';
    message = 'Token inválido ou ausente';
  } else if (err.name === 'ForbiddenError') {
    statusCode = 403;
    errorType = 'FORBIDDEN';
    message = 'Acesso negado';
  } else if (err.name === 'NotFoundError') {
    statusCode = 404;
    errorType = 'NOT_FOUND';
    message = 'Recurso não encontrado';
  } else if (err.name === 'ConflictError') {
    statusCode = 409;
    errorType = 'CONFLICT';
    message = 'Conflito de dados';
  }

  // Resposta padronizada
  const errorResponse = {
    success: false,
    error: {
      type: errorType,
      message: message,
      timestamp: new Date().toISOString(),
      path: req.path,
      method: req.method
    }
  };

  // Adicionar detalhes apenas em desenvolvimento
  if (process.env.NODE_ENV === 'development') {
    errorResponse.error.details = err.message;
    errorResponse.error.stack = err.stack;
  }

  res.status(statusCode).json(errorResponse);
});
```

### Checklist para APIs bem projetadas

#### ✅ Boas práticas obrigatórias:
- **Use códigos específicos** em vez de apenas 200 ou 500
- **Seja consistente** no uso dos códigos em toda a API
- **Inclua mensagens descritivas** junto com os códigos
- **Documente os códigos** que sua API utiliza
- **Use o código 202** para operações assíncronas longas
- **Diferencie 401 (não autenticado) e 403 (não autorizado)**
- **Implemente rate limiting** com código 429
- **Use 422** para erros de validação semântica
- **Inclua headers relevantes** (Location, Retry-After, etc.)
- **Padronize formato de erro** em toda a API

#### ❌ Erros críticos a evitar:
- ❌ Retornar 200 OK com mensagem de erro no corpo
- ❌ Usar 404 para erros de autorização (use 403)
- ❌ Expor detalhes técnicos de erros 500 em produção
- ❌ Ignorar os códigos 429 (rate limiting) e 405 (método não permitido)
- ❌ Usar 500 para erros de validação (use 400 ou 422)
- ❌ Não documentar códigos de status possíveis
- ❌ Inconsistência entre endpoints similares
- ❌ Não incluir informações úteis para debugging

### Testando Códigos de Status

```javascript
// Exemplo de testes com Jest
describe('API Status Codes', () => {
  test('GET /api/usuarios deve retornar 200', async () => {
    const response = await request(app).get('/api/usuarios');
    expect(response.status).toBe(200);
    expect(response.body).toHaveProperty('dados');
  });

  test('POST /api/usuarios deve retornar 201', async () => {
    const novoUsuario = { nome: 'Teste', email: 'teste@exemplo.com' };
    const response = await request(app)
      .post('/api/usuarios')
      .send(novoUsuario);
    
    expect(response.status).toBe(201);
    expect(response.headers.location).toBeDefined();
  });

  test('GET /api/usuarios/999 deve retornar 404', async () => {
    const response = await request(app).get('/api/usuarios/999');
    expect(response.status).toBe(404);
    expect(response.body.erro).toBe('USER_NOT_FOUND');
  });

  test('POST sem token deve retornar 401', async () => {
    const response = await request(app)
      .post('/api/perfil')
      .send({ nome: 'Teste' });
    
    expect(response.status).toBe(401);
    expect(response.body.erro).toBe('MISSING_TOKEN');
  });
});
```

### Recursos Adicionais

- **RFC 7231**: Especificação oficial dos códigos de status HTTP
- **MDN Web Docs**: Documentação completa sobre códigos HTTP
- **REST API Design**: Guias de boas práticas para APIs RESTful
- **Postman/Insomnia**: Ferramentas para testar códigos de status

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=footer"/>