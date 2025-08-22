# 📋 Guia Completo de Integração VisaNetPay
## GitHub + Vercel + MGX - Passo a Passo Detalhado

---

## 📝 **ÍNDICE**

1. [Pré-requisitos](#pré-requisitos)
2. [Configuração do GitHub](#configuração-do-github)
3. [Integração com Vercel](#integração-com-vercel)
4. [Workflow de Desenvolvimento](#workflow-de-desenvolvimento)
5. [Gerenciamento de Atualizações](#gerenciamento-de-atualizações)
6. [Monitoramento e Manutenção](#monitoramento-e-manutenção)
7. [Integração com MGX](#integração-com-mgx)
8. [Troubleshooting Comum](#troubleshooting-comum)

---

## 🛠️ **PRÉ-REQUISITOS**

### **1. Node.js (versão 18 ou superior)**
- **Acesse**: https://nodejs.org/
- **Baixe** a versão LTS (Long Term Support)
- **Instale** seguindo as instruções do instalador
- **Verificar instalação**:
```bash
node --version
npm --version
```
*Resultado esperado: v18.17.0+ e 9.6.7+*

### **2. Git**
- **Acesse**: https://git-scm.com/
- **Baixe e instale** para seu sistema operacional
- **Verificar instalação**:
```bash
git --version
```
*Resultado esperado: git version 2.40.0+*

### **3. Editor de Código (VS Code recomendado)**
- **Acesse**: https://code.visualstudio.com/
- **Baixe e instale** a versão para seu sistema

---

## 🐱 **CONFIGURAÇÃO DO GITHUB**

### **Passo 1: Criar Conta no GitHub**

1. **Acesse**: https://github.com/
2. **Clique em "Sign up"** (Criar conta)
3. **Preencha os dados**:
   - **Username**: nome único (ex: joao-silva-dev)
   - **Email**: email válido que você acessa regularmente
   - **Senha**: mínimo 8 caracteres, incluindo números e símbolos
4. **Complete a verificação**: Resolva o captcha ou puzzle de verificação
5. **Confirme seu email**: Verifique sua caixa de entrada e clique no link de confirmação

### **Passo 2: Configurar Git no Computador**

Abra o terminal/prompt de comando e execute:

```bash
# Configure sua identidade (substitua pelos seus dados reais)
git config --global user.name "João Silva"
git config --global user.email "joao.silva@email.com"

# Verificar se as configurações foram aplicadas
git config --list | grep user
```

**💡 Explicação**: Essas informações aparecerão em todos os seus commits, identificando você como autor das mudanças.

### **Passo 3: Criar Repositório no GitHub**

1. **No GitHub**, clique no **botão verde "New"** (canto superior direito)
2. **Preencha as informações**:
   - **Repository name**: `visanetpay-banking-system`
   - **Description**: `Sistema bancário avançado com integração fiat e crypto`
   - **Visibilidade**: 
     - ✅ **Private** (recomendado para projetos comerciais)
     - ❌ Public (apenas se quiser código aberto)
   - **⚠️ IMPORTANTE**: 
     - **NÃO marque** "Add a README file"
     - **NÃO marque** "Add .gitignore"
     - **NÃO marque** "Choose a license"
3. **Clique em "Create repository"**

### **Passo 4: Configurar Autenticação SSH**

#### **Gerar e configurar chave SSH (Recomendado)**

```bash
# 1. Gerar nova chave SSH (substitua pelo SEU email)
ssh-keygen -t ed25519 -C "joao.silva@email.com"

# Quando perguntado:
# - Onde salvar: Pressione ENTER (usar padrão ~/.ssh/id_ed25519)
# - Passphrase: Pode deixar vazio ou criar senha extra

# 2. Iniciar ssh-agent
eval "$(ssh-agent -s)"

# 3. Adicionar chave ao agente
ssh-add ~/.ssh/id_ed25519

# 4. Copiar chave pública
# Windows (Git Bash):
cat ~/.ssh/id_ed25519.pub | clip

# macOS:
pbcopy < ~/.ssh/id_ed25519.pub

# Linux:
cat ~/.ssh/id_ed25519.pub
# (copie manualmente o resultado)
```

#### **Adicionar chave no GitHub**

1. GitHub.com → **Settings** → **SSH and GPG keys**
2. **New SSH key**
3. **Title**: "Meu Computador Principal"
4. **Key**: Cole a chave copiada
5. **Add SSH key**

#### **Testar conexão**
```bash
ssh -T git@github.com
```
*Resultado esperado: "Hi USERNAME! You've successfully authenticated..."*

### **Passo 5: Upload do Projeto**

```bash
# 1. Navegar para o diretório do projeto
cd /data/chats/2b0ae79eb8e24e0b8b3fe17ae12f924d/workspace

# 2. Verificar localização (deve mostrar arquivos do VisaNetPay)
ls -la

# 3. Inicializar Git
git init

# 4. Adicionar todos os arquivos
git add .

# 5. Primeiro commit
git commit -m "Initial commit: VisaNetPay Banking System"

# 6. Renomear branch para main
git branch -M main

# 7. Conectar ao repositório remoto (substitua SEU_USERNAME)
git remote add origin git@github.com:SEU_USERNAME/visanetpay-banking-system.git

# 8. Enviar para GitHub
git push -u origin main
```

**🎉 Sucesso!** Acesse seu repositório no GitHub para ver os arquivos.

---

## ⚡ **INTEGRAÇÃO COM VERCEL**

### **Passo 1: Criar Conta na Vercel**

1. **Acesse**: https://vercel.com/
2. **"Sign Up"** → **"Continue with GitHub"** (recomendado)
3. **Autorize** a Vercel no GitHub
4. **Complete perfil** se solicitado

### **Passo 2: Importar Repositório**

1. **Dashboard Vercel** → **"Import Project"**
2. **Encontre** `visanetpay-banking-system`
3. **Clique "Import"**

### **Passo 3: Configurar Deploy**

1. **Framework**: Vercel detecta "Next.js" automaticamente ✅
2. **Root Directory**: Como o Next.js está em `shadcn-ui/`:
   - Clique **"Edit"** em Root Directory
   - Digite: `shadcn-ui`
   - **"Save"**

3. **Build Settings** (normalmente automático):
   - Build Command: `npm run build`
   - Output Directory: `.next`
   - Install Command: `npm install`

### **Passo 4: Variáveis de Ambiente (se necessário)**

Se usar Supabase ou APIs externas:

```env
# Clique em "Environment Variables" e adicione:
NEXT_PUBLIC_APP_URL=https://seu-app.vercel.app
NEXT_PUBLIC_SUPABASE_URL=sua_url_supabase
NEXT_PUBLIC_SUPABASE_ANON_KEY=sua_chave_supabase
```

**💡 Regra**: `NEXT_PUBLIC_` = visível no navegador, sem prefixo = apenas servidor

### **Passo 5: Deploy**

1. **Clique "Deploy"**
2. **Aguarde** (1-3 minutos)
3. **Resultado**: URL como `https://visanetpay-banking-system.vercel.app`

**✅ Deploy automático configurado!** Toda mudança na branch `main` = novo deploy.

---

## 🔄 **WORKFLOW DE DESENVOLVIMENTO**

### **Conceitos Básicos**

- **Repository**: Pasta com histórico de mudanças
- **Branch**: Linha paralela de desenvolvimento  
- **Commit**: Checkpoint das mudanças
- **Push**: Enviar para GitHub
- **Pull**: Baixar do GitHub
- **Merge**: Juntar branches

### **Rotina Diária**

#### **1. Sempre Começar Atualizando**
```bash
git checkout main
git pull origin main
```

#### **2. Criar Branch para Nova Feature**
```bash
# Exemplos de nomes:
git checkout -b feature/adicionar-pix-payment
git checkout -b fix/corrigir-login-bug
git checkout -b hotfix/erro-critico
```

#### **3. Desenvolver Localmente**
```bash
cd shadcn-ui
npm install  # Primeira vez
npm run dev  # Desenvolvimento
# Acesse: http://localhost:3000
```

#### **4. Fazer Commits**
```bash
git status                    # Ver mudanças
git add .                     # Adicionar arquivos
git commit -m "feat: descrição clara do que foi implementado"
```

**Convenção de Mensagens:**
- `feat:` - Nova funcionalidade
- `fix:` - Correção de bug
- `docs:` - Documentação
- `style:` - Formatação/UI
- `refactor:` - Refatoração
- `test:` - Testes

#### **5. Enviar para GitHub**
```bash
git push origin feature/nome-da-branch
```

#### **6. Criar Pull Request**

No GitHub:
1. Banner "Compare & pull request"
2. Preencher descrição detalhada
3. "Create pull request"

#### **7. Review e Merge**
1. Revisar código
2. "Merge pull request"
3. Deletar branch (limpeza)

#### **8. Atualizar Local**
```bash
git checkout main
git pull origin main
git branch -d feature/nome-da-branch  # Limpar
```

---

## 🔧 **GERENCIAMENTO DE ATUALIZAÇÕES**

### **1. Hotfix (Correção Urgente)**

```bash
# Partir da main atualizada
git checkout main
git pull origin main

# Branch de hotfix
git checkout -b hotfix/corrigir-erro-critico

# Fazer correção mínima
# ... editar arquivos essenciais ...

# Testar
npm run dev
npm run build

# Commit urgente
git add .
git commit -m "fix: corrigir erro crítico no processamento de pagamentos"
git push origin hotfix/corrigir-erro-critico

# PR com prioridade alta → Merge imediato
```

**Meta**: 15-30 minutos da identificação ao deploy.

### **2. Novas Funcionalidades**

```bash
git checkout -b feature/nova-funcionalidade

# Desenvolvimento completo:
# - Interface
# - Lógica  
# - Testes
# - Documentação

git commit -m "feat: implementar [descrição da funcionalidade]"
git push origin feature/nova-funcionalidade
```

### **3. Atualizações de Dependências**

```bash
# Verificar desatualizadas
npm outdated

# Atualizar específica (seguro)
npm update react

# Testar sempre após atualizar
npm run build
npm run dev

# Se OK, commitar
git add package*.json
git commit -m "chore: atualizar dependências de segurança"
```

### **4. Processo de Rollback**

#### **Método 1: Vercel (30 segundos)**
1. Vercel Dashboard → Deployments
2. Deploy anterior estável → "Promote to Production"

#### **Método 2: Git**
```bash
# Ver histórico
git log --oneline

# Reverter commit (seguro)
git revert HASH_DO_COMMIT_PROBLEMÁTICO
git push origin main
```

---

## 📊 **MONITORAMENTO E MANUTENÇÃO**

### **1. Dashboard Vercel**

#### **Métricas Importantes**
- **Vercel.com** → **Seu projeto** → **Analytics**:
  - Page views
  - Performance metrics
  - Error rates
  - Geographic data

#### **Logs de Deploy**
- **Deployments** tab:
  - Status: ✅ Success, ❌ Failed
  - Build time
  - Detailed logs

### **2. Configurar Alertas**

**Vercel Settings → Notifications:**
```
✅ Deploy Success   - Confirmação
❌ Deploy Failed    - CRÍTICO
⚠️  Performance     - Monitoramento
🔒 Security        - Vulnerabilidades
```

### **3. Monitoramento Externo**

**UptimeRobot** (gratuito): https://uptimerobot.com/
```
Monitor Type: HTTP(s)
URL: https://seu-app.vercel.app  
Interval: 5 minutes
Alert: Email quando site sair do ar
```

### **4. Debug Comum**

#### **"Build failed"**
```bash
# Testar localmente primeiro
npm run build

# Verificar logs na Vercel
# Comum: dependências faltando, erros TypeScript
```

#### **"Module not found"**
```bash
# Limpar e reinstalar
rm -rf node_modules package-lock.json
npm install
```

#### **Performance Lenta**
```bash
# Analisar bundle
npm run build
# Verificar tamanho em .next/static/

# Otimizações:
# - Lazy loading
# - Otimizar imagens  
# - Code splitting
```

---

## 🚀 **INTEGRAÇÃO COM MGX**

### **O que é MGX**

MGX é uma plataforma de desenvolvimento com IA que oferece:
- Code review automático
- Sugestões de melhorias
- Análise de segurança
- Desenvolvimento assistido

### **Conectar ao MGX**

1. **Acesse plataforma MGX**
2. **Import Project** → **From GitHub** 
3. **Selecionar** `visanetpay-banking-system`
4. **Configurar**:
   - Language: TypeScript
   - Framework: Next.js
   - Type: Financial Application

### **Workflow com MGX**

#### **Desenvolvimento Assistido**
```bash
# MGX pode sugerir:
# - Estrutura de código
# - Melhores práticas
# - Otimizações de segurança
# - Testes automáticos
```

#### **Code Review IA**
Após commits, MGX analisa:
- 🔒 Segurança: Vulnerabilidades, chaves expostas
- ⚡ Performance: Otimizações possíveis  
- 🏗️ Arquitetura: Padrões de código
- 🧪 Testes: Cobertura e qualidade

### **Comandos MGX Úteis**
```bash
# Sincronizar projeto
mgx sync

# Análise completa
mgx analyze --full-report

# Deploy com verificações
mgx deploy --env staging --verify

# Gerar documentação
mgx docs generate
```

---

## 🛠️ **TROUBLESHOOTING COMUM**

### **1. Problemas Git**

#### **"Permission denied"**
```bash
# Verificar SSH
ssh-add -l
ssh-add ~/.ssh/id_ed25519
ssh -T git@github.com
```

#### **"Merge conflicts"**
```bash
# Ver conflitos
git status

# Resolver manualmente (procurar por <<<<<<< e >>>>>>>)
# Após resolver:
git add .
git commit -m "resolve: conflitos de merge"
```

### **2. Problemas Node.js**

#### **"Module not found"**
```bash
rm -rf node_modules package-lock.json
npm install
```

#### **"Out of memory"**
```bash
export NODE_OPTIONS="--max-old-space-size=4096"
npm run build
```

### **3. Problemas Vercel**

#### **"Build failed"**
```bash
# Testar local
npm run build

# Verificar logs Vercel Dashboard
# Soluções comuns:
npm audit fix
npm update
```

#### **"Environment variables"**
- Verificar prefixo `NEXT_PUBLIC_` para variáveis do cliente
- Verificar se estão definidas no Vercel Dashboard

### **4. Problemas Performance**

#### **Site lento**
```bash
# Analisar bundle
npm run build
ls -la .next/static/chunks/

# Otimizações:
# - next/image para imagens
# - dynamic() para lazy loading  
# - Remover dependências não usadas
```

---

## ✅ **CHECKLIST FINAL**

### **Configuração Inicial**
- [ ] Node.js 18+ instalado
- [ ] Git configurado  
- [ ] Conta GitHub criada
- [ ] SSH configurado
- [ ] Repositório criado
- [ ] Projeto enviado para GitHub
- [ ] Conta Vercel criada
- [ ] Deploy funcionando
- [ ] URL acessível

### **Workflow Estabelecido**  
- [ ] Entende git flow básico
- [ ] Sabe criar branches
- [ ] Consegue fazer commits
- [ ] Sabe criar Pull Requests
- [ ] Deploy automático funciona

### **Monitoramento Ativo**
- [ ] Alertas Vercel configurados
- [ ] Uptime monitoring externo
- [ ] Conhece logs de debug
- [ ] Sabe fazer rollback

### **Segurança**
- [ ] Two-factor authentication
- [ ] Chaves SSH seguras
- [ ] Variáveis ambiente protegidas
- [ ] Dependências atualizadas

**🎉 Parabéns! Seu VisaNetPay está com infraestrutura profissional completa!**

---

## 📚 **RECURSOS ADICIONAIS**

### **Documentação Oficial**
- **Next.js**: https://nextjs.org/docs
- **Vercel**: https://vercel.com/docs
- **GitHub**: https://docs.github.com/pt

### **Comunidades**
- **Discord**: Next.js, Vercel communities
- **Reddit**: r/nextjs, r/webdev
- **Stack Overflow**: Tags [nextjs], [vercel]

### **Cursos Recomendados**
- "Git & GitHub para Iniciantes" (YouTube)
- "Next.js Full Course" (freeCodeCamp)  
- "Vercel Deploy Guide" (Vercel Learning)

---

*Guia criado para o projeto VisaNetPay - Versão 1.0*
*Última atualização: Dezembro 2024*