<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=header"/>

# 📦 Gerenciadores de Pacotes

## 🔍 O que são Gerenciadores de Pacotes?
Os gerenciadores de pacotes são ferramentas essenciais no ecossistema Node.js que facilitam a instalação, remoção e atualização de dependências em projetos. Eles permitem compartilhar e reutilizar código de forma eficiente.

## 🛠️ Principais Gerenciadores

### 📚 npm (Node Package Manager)
O npm é o gerenciador de pacotes padrão que vem instalado com o Node.js.

#### Comandos básicos:
- **📥 Instalação de pacotes**: `npm install pacote` ou `npm i pacote`
- **🌐 Instalação global**: `npm install -g pacote`
- **🔧 Instalação para desenvolvimento**: `npm install --save-dev pacote` ou `npm i -D pacote`
- **🔄 Atualização de pacotes**: `npm update pacote`
- **🚮 Remoção de pacotes**: `npm uninstall pacote`
- **▶️ Executar scripts**: `npm run script-name`

#### 📄 Arquivos de configuração:
- **package.json**: Contém metadados do projeto e lista de dependências
- **package-lock.json**: Garante instalações consistentes entre ambientes

```json
// Exemplo de package.json
{
  "name": "meu-projeto",
  "version": "1.0.0",
  "description": "Descrição do projeto",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js",
    "test": "jest"
  },
  "dependencies": {
    "express": "^4.18.2"
  },
  "devDependencies": {
    "nodemon": "^2.0.22",
    "jest": "^29.5.0"
  }
}
```

### 🧶 Yarn
Yarn é uma alternativa ao npm, conhecido por seu melhor desempenho e confiabilidade.

#### Comandos básicos:
- **📥 Instalação de pacotes**: `yarn add pacote`
- **🌐 Instalação global**: `yarn global add pacote`
- **🔧 Instalação para desenvolvimento**: `yarn add --dev pacote`
- **🔄 Atualização de pacotes**: `yarn upgrade pacote`
- **🚮 Remoção de pacotes**: `yarn remove pacote`
- **▶️ Executar scripts**: `yarn script-name`

#### 📄 Arquivos de configuração:
- **yarn.lock**: Similar ao package-lock.json, garante consistência nas instalações

### 📌 pnpm
Uma alternativa mais recente que economiza espaço em disco utilizando links simbólicos para evitar duplicação de pacotes.

#### Vantagens:
- **💾 Economia de espaço**: Compartilha pacotes entre projetos
- **⚡ Instalação rápida**: Geralmente mais rápido que npm e yarn
- **🔄 Compatibilidade**: Usa os mesmos comandos do npm

## 📊 Comparação entre Gerenciadores

| Característica | npm | Yarn | pnpm |
|----------------|-----|------|------|
| Velocidade | Boa | Muito boa | Excelente |
| Espaço em disco | Alto | Alto | Baixo |
| Cache | Sim | Sim | Sim |
| Instalação paralela | Sim | Sim | Sim |
| Workspace | Sim | Sim | Sim |

## 💡 Dicas e Boas Práticas

- **🔒 Fixe as versões** das dependências para evitar problemas com atualizações incompatíveis
- **📋 Documente as dependências** adicionando-as ao package.json
- **👥 Use scripts personalizados** para automatizar tarefas comuns
- **⚠️ Audite sua segurança** regularmente com `npm audit` ou `yarn audit`
- **🧹 Limpe o cache** ocasionalmente para liberar espaço com `npm cache clean --force`

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=339933&height=120&section=footer"/>
