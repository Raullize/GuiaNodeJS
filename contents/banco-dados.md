<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=header"/>

# Conectando ao Banco de Dados

## Bancos de Dados com Node.js
O Node.js é altamente versátil quando se trata de interação com bancos de dados, permitindo conexões com praticamente qualquer sistema de gerenciamento de dados, seja SQL ou NoSQL.

### Tipos de Bancos de Dados

#### Bancos de Dados Relacionais (SQL)
- **MySQL**: Sistema robusto e amplamente utilizado
- **PostgreSQL**: Banco relacional avançado com recursos poderosos
- **SQLite**: Solução leve ideal para aplicações menores
- **MariaDB**: Fork do MySQL com recursos adicionais

#### Bancos de Dados NoSQL
- **MongoDB**: Banco orientado a documentos (JSON/BSON)
- **Redis**: Armazenamento chave-valor em memória
- **Cassandra**: Banco de dados distribuído para grandes volumes
- **Firebase Firestore**: Banco NoSQL em nuvem da Google

## Conectando a Bancos de Dados

### MongoDB com Mongoose
Mongoose é uma biblioteca ODM (Object Data Modeling) para MongoDB e Node.js que proporciona uma solução direta para modelar dados de aplicação.

#### Instalação
```bash
npm install mongoose
```

#### Configuração da Conexão
```javascript
// db/connection.js
const mongoose = require('mongoose');
require('dotenv').config(); // Para carregar variáveis de ambiente

const conectarBancoDados = async () => {
  try {
    await mongoose.connect(process.env.MONGODB_URI, {
      useNewUrlParser: true,
      useUnifiedTopology: true
    });
    console.log('MongoDB conectado com sucesso!');
  } catch (erro) {
    console.error('Erro ao conectar ao MongoDB:', erro.message);
    process.exit(1); // Encerra a aplicação em caso de erro
  }
};

module.exports = conectarBancoDados;
```

#### Definindo um Modelo (Schema)
```javascript
// models/Usuario.js
const mongoose = require('mongoose');

const UsuarioSchema = new mongoose.Schema({
  nome: {
    type: String,
    required: [true, 'O nome é obrigatório'],
    trim: true
  },
  email: {
    type: String,
    required: [true, 'O e-mail é obrigatório'],
    unique: true,
    match: [
      /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/,
      'Por favor, forneça um e-mail válido'
    ]
  },
  senha: {
    type: String,
    required: [true, 'A senha é obrigatória'],
    minlength: [6, 'A senha deve ter pelo menos 6 caracteres'],
    select: false // Não retorna a senha nas consultas
  },
  dataCriacao: {
    type: Date,
    default: Date.now
  },
  ativo: {
    type: Boolean,
    default: true
  }
});

// Método para comparar senha
UsuarioSchema.methods.comparaSenha = async function(senhaInformada) {
  // Implementação de verificação de senha...
  return true; // Substitua por lógica real
};

// Hook de pré-salvamento para hash de senha
UsuarioSchema.pre('save', async function(next) {
  // Lógica para hash de senha...
  next();
});

module.exports = mongoose.model('Usuario', UsuarioSchema);
```

#### Operações CRUD
```javascript
// controllers/usuariosController.js
const Usuario = require('../models/Usuario');

// Criar usuário
exports.criarUsuario = async (req, res) => {
  try {
    const novoUsuario = await Usuario.create(req.body);
    
    res.status(201).json({
      sucesso: true,
      data: novoUsuario
    });
  } catch (erro) {
    res.status(400).json({
      sucesso: false,
      mensagem: erro.message
    });
  }
};

// Buscar todos os usuários
exports.buscarUsuarios = async (req, res) => {
  try {
    const usuarios = await Usuario.find();
    
    res.status(200).json({
      sucesso: true,
      quantidade: usuarios.length,
      data: usuarios
    });
  } catch (erro) {
    res.status(500).json({
      sucesso: false,
      mensagem: 'Erro ao buscar usuários'
    });
  }
};

// Buscar usuário por ID
exports.buscarUsuarioPorId = async (req, res) => {
  try {
    const usuario = await Usuario.findById(req.params.id);
    
    if (!usuario) {
      return res.status(404).json({
        sucesso: false,
        mensagem: 'Usuário não encontrado'
      });
    }
    
    res.status(200).json({
      sucesso: true,
      data: usuario
    });
  } catch (erro) {
    res.status(500).json({
      sucesso: false,
      mensagem: 'Erro ao buscar usuário'
    });
  }
};

// Atualizar usuário
exports.atualizarUsuario = async (req, res) => {
  try {
    const usuario = await Usuario.findByIdAndUpdate(
      req.params.id,
      req.body,
      { new: true, runValidators: true }
    );
    
    if (!usuario) {
      return res.status(404).json({
        sucesso: false,
        mensagem: 'Usuário não encontrado'
      });
    }
    
    res.status(200).json({
      sucesso: true,
      data: usuario
    });
  } catch (erro) {
    res.status(500).json({
      sucesso: false,
      mensagem: 'Erro ao atualizar usuário'
    });
  }
};

// Excluir usuário
exports.excluirUsuario = async (req, res) => {
  try {
    const usuario = await Usuario.findByIdAndDelete(req.params.id);
    
    if (!usuario) {
      return res.status(404).json({
        sucesso: false,
        mensagem: 'Usuário não encontrado'
      });
    }
    
    res.status(200).json({
      sucesso: true,
      mensagem: 'Usuário excluído com sucesso'
    });
  } catch (erro) {
    res.status(500).json({
      sucesso: false,
      mensagem: 'Erro ao excluir usuário'
    });
  }
};
```

### MySQL com Sequelize
Sequelize é um ORM (Object-Relational Mapping) baseado em Promises para Node.js, que suporta PostgreSQL, MySQL, MariaDB, SQLite e Microsoft SQL Server.

#### Instalação
```bash
npm install sequelize mysql2
# ou para PostgreSQL: npm install sequelize pg pg-hstore
```

#### Configuração da Conexão
```javascript
// db/connection.js
const { Sequelize } = require('sequelize');
require('dotenv').config();

const sequelize = new Sequelize(
  process.env.DB_NAME,
  process.env.DB_USER,
  process.env.DB_PASSWORD,
  {
    host: process.env.DB_HOST,
    dialect: 'mysql', // mysql, postgres, sqlite, mariadb, mssql
    port: process.env.DB_PORT || 3306,
    logging: false, // Desativa logs SQL no console
    pool: {
      max: 5,
      min: 0,
      acquire: 30000,
      idle: 10000
    }
  }
);

const testarConexao = async () => {
  try {
    await sequelize.authenticate();
    console.log('Conexão com o banco de dados estabelecida com sucesso!');
  } catch (erro) {
    console.error('Erro ao conectar ao banco de dados:', erro.message);
    process.exit(1);
  }
};

module.exports = { sequelize, testarConexao };
```

#### Definindo um Modelo
```javascript
// models/Usuario.js
const { DataTypes } = require('sequelize');
const { sequelize } = require('../db/connection');
const bcrypt = require('bcrypt');

const Usuario = sequelize.define('Usuario', {
  id: {
    type: DataTypes.INTEGER,
    primaryKey: true,
    autoIncrement: true
  },
  nome: {
    type: DataTypes.STRING,
    allowNull: false,
    validate: {
      notEmpty: {
        msg: 'O nome é obrigatório'
      }
    }
  },
  email: {
    type: DataTypes.STRING,
    allowNull: false,
    unique: true,
    validate: {
      isEmail: {
        msg: 'Por favor, forneça um e-mail válido'
      }
    }
  },
  senha: {
    type: DataTypes.STRING,
    allowNull: false,
    validate: {
      len: {
        args: [6, 100],
        msg: 'A senha deve ter pelo menos 6 caracteres'
      }
    }
  },
  ativo: {
    type: DataTypes.BOOLEAN,
    defaultValue: true
  }
}, {
  tableName: 'usuarios',
  timestamps: true, // Cria campos createdAt e updatedAt
  hooks: {
    beforeCreate: async (usuario) => {
      if (usuario.senha) {
        const salt = await bcrypt.genSalt(10);
        usuario.senha = await bcrypt.hash(usuario.senha, salt);
      }
    },
    beforeUpdate: async (usuario) => {
      if (usuario.changed('senha')) {
        const salt = await bcrypt.genSalt(10);
        usuario.senha = await bcrypt.hash(usuario.senha, salt);
      }
    }
  }
});

// Método de instância para verificar senha
Usuario.prototype.verificaSenha = async function(senhaFornecida) {
  return await bcrypt.compare(senhaFornecida, this.senha);
};

module.exports = Usuario;
```

#### Inicialização de Modelos e Sincronização
```javascript
// models/index.js
const { sequelize } = require('../db/connection');
const Usuario = require('./Usuario');

// Definir relações entre modelos, se necessário
// Usuario.hasMany(OutroModelo, { foreignKey: 'usuarioId' });

// Sincronizar modelos com o banco de dados
const sincronizarModelos = async () => {
  try {
    // { force: true } recria as tabelas (cuidado! Apaga dados existentes)
    // { alter: true } faz alterações na estrutura para alinhar com o modelo
    await sequelize.sync({ alter: true });
    console.log('Modelos sincronizados com o banco de dados');
  } catch (erro) {
    console.error('Erro ao sincronizar modelos:', erro.message);
    process.exit(1);
  }
};

module.exports = {
  Usuario,
  sincronizarModelos
};
```

#### Operações CRUD com Sequelize
```javascript
// controllers/usuariosController.js
const { Usuario } = require('../models');

// Criar usuário
exports.criarUsuario = async (req, res) => {
  try {
    const novoUsuario = await Usuario.create(req.body);
    
    // Remove senha da resposta
    const usuarioResponse = novoUsuario.toJSON();
    delete usuarioResponse.senha;
    
    res.status(201).json({
      sucesso: true,
      data: usuarioResponse
    });
  } catch (erro) {
    res.status(400).json({
      sucesso: false,
      mensagem: erro.message
    });
  }
};

// Buscar todos os usuários
exports.buscarUsuarios = async (req, res) => {
  try {
    const usuarios = await Usuario.findAll({
      attributes: { exclude: ['senha'] }
    });
    
    res.status(200).json({
      sucesso: true,
      quantidade: usuarios.length,
      data: usuarios
    });
  } catch (erro) {
    res.status(500).json({
      sucesso: false,
      mensagem: 'Erro ao buscar usuários'
    });
  }
};

// Buscar usuário por ID
exports.buscarUsuarioPorId = async (req, res) => {
  try {
    const usuario = await Usuario.findByPk(req.params.id, {
      attributes: { exclude: ['senha'] }
    });
    
    if (!usuario) {
      return res.status(404).json({
        sucesso: false,
        mensagem: 'Usuário não encontrado'
      });
    }
    
    res.status(200).json({
      sucesso: true,
      data: usuario
    });
  } catch (erro) {
    res.status(500).json({
      sucesso: false,
      mensagem: 'Erro ao buscar usuário'
    });
  }
};

// Atualizar usuário
exports.atualizarUsuario = async (req, res) => {
  try {
    const [atualizou] = await Usuario.update(req.body, {
      where: { id: req.params.id },
      individualHooks: true // Assegura que hooks beforeUpdate sejam executados
    });
    
    if (atualizou === 0) {
      return res.status(404).json({
        sucesso: false,
        mensagem: 'Usuário não encontrado'
      });
    }
    
    const usuarioAtualizado = await Usuario.findByPk(req.params.id, {
      attributes: { exclude: ['senha'] }
    });
    
    res.status(200).json({
      sucesso: true,
      data: usuarioAtualizado
    });
  } catch (erro) {
    res.status(500).json({
      sucesso: false,
      mensagem: 'Erro ao atualizar usuário'
    });
  }
};

// Excluir usuário
exports.excluirUsuario = async (req, res) => {
  try {
    const excluiu = await Usuario.destroy({
      where: { id: req.params.id }
    });
    
    if (excluiu === 0) {
      return res.status(404).json({
        sucesso: false,
        mensagem: 'Usuário não encontrado'
      });
    }
    
    res.status(200).json({
      sucesso: true,
      mensagem: 'Usuário excluído com sucesso'
    });
  } catch (erro) {
    res.status(500).json({
      sucesso: false,
      mensagem: 'Erro ao excluir usuário'
    });
  }
};
```

## Boas Práticas para Trabalhar com Bancos de Dados

### Segurança
1. **Nunca exponha credenciais do banco de dados** no código
   - Use variáveis de ambiente (.env)
   ```javascript
   // .env
   DB_HOST=localhost
   DB_USER=usuario
   DB_PASSWORD=senha_segura
   DB_NAME=meu_banco
   ```

2. **Previna injeção SQL**
   - Use ORM/ODM (Sequelize, Mongoose) ou consultas parametrizadas
   - Nunca concatene strings para formar consultas SQL

3. **Valide todos os dados** antes de manipular o banco de dados

### Performance
1. **Use índices adequadamente** para consultas frequentes
   ```javascript
   // Exemplo com MongoDB/Mongoose
   const UsuarioSchema = new mongoose.Schema({
     email: {
       type: String,
       required: true,
       unique: true,
       index: true // Adiciona índice para buscas mais rápidas
     }
   });
  
   // Exemplo com Sequelize
   const Usuario = sequelize.define('Usuario', {
     email: {
       type: DataTypes.STRING,
       unique: true,
       allowNull: false
     }
   }, {
     indexes: [{ fields: ['email'] }]
   });
   ```

2. **Evite carregar dados desnecessários**
   - Selecione apenas colunas necessárias
   - Use paginação para grandes conjuntos de dados

3. **Utilize caching quando apropriado**
   - Redis é uma excelente opção para caching

### Modelagem
1. **Mantenha um bom equilíbrio entre normalização e desnormalização**
2. **Defina relacionamentos adequadamente**
3. **Use transações** para operações que modificam múltiplas entidades

### Migrations
Migrations são essenciais para controlar alterações no esquema do banco de dados.

#### Exemplo com Sequelize CLI
```bash
npm install -g sequelize-cli
npm install --save-dev sequelize-cli

# Inicializa estrutura para migrations
npx sequelize-cli init

# Cria uma nova migration
npx sequelize-cli migration:generate --name criar-tabela-usuarios

# Executa migrations pendentes
npx sequelize-cli db:migrate

# Reverte última migration
npx sequelize-cli db:migrate:undo
```

Exemplo de arquivo de migration:
```javascript
// migrations/xxxxx-criar-tabela-usuarios.js
'use strict';

module.exports = {
  up: async (queryInterface, Sequelize) => {
    await queryInterface.createTable('usuarios', {
      id: {
        allowNull: false,
        autoIncrement: true,
        primaryKey: true,
        type: Sequelize.INTEGER
      },
      nome: {
        type: Sequelize.STRING,
        allowNull: false
      },
      email: {
        type: Sequelize.STRING,
        allowNull: false,
        unique: true
      },
      senha: {
        type: Sequelize.STRING,
        allowNull: false
      },
      ativo: {
        type: Sequelize.BOOLEAN,
        defaultValue: true
      },
      createdAt: {
        allowNull: false,
        type: Sequelize.DATE
      },
      updatedAt: {
        allowNull: false,
        type: Sequelize.DATE
      }
    });
  },

  down: async (queryInterface, Sequelize) => {
    await queryInterface.dropTable('usuarios');
  }
};
```

## Tecnologias de Banco de Dados em Evolução

### ORMs/ODMs Alternativos
- **Prisma**: ORM de próxima geração com foco em type safety
- **TypeORM**: ORM para TypeScript e JavaScript
- **Knex.js**: Query builder SQL flexível

### Bancos de Dados Modernos
- **Fauna**: Banco de dados nativo para serverless
- **Supabase**: Alternativa de código aberto ao Firebase
- **Deno KV**: Armazenamento chave-valor para Deno

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=footer"/>