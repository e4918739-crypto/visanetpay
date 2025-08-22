# 🔐 CONFIGURAÇÃO GITHUB SECRETS - DEPLOY AUTOMÁTICO

## 📋 **GUIA COMPLETO PARA CONFIGURAR SECRETS NO GITHUB**

Este guia mostra como configurar as credenciais necessárias para deploy automático do VisaNetPay no GitHub Actions.

---

## 🎯 **PRÉ-REQUISITOS**

✅ Repositório GitHub criado para o projeto VisaNetPay  
✅ Conta Vercel configurada  
✅ Credenciais validadas (todas as 5 chaves)  

---

## 🚀 **PASSO A PASSO**

### **1. Acessar Configurações do Repositório**

1. Acesse seu repositório GitHub
2. Clique em **"Settings"** (Configurações)
3. No menu lateral, clique em **"Secrets and variables"**
4. Selecione **"Actions"**

### **2. Adicionar Secrets Obrigatórias**

Clique em **"New repository secret"** para cada uma das seguintes:

#### 🔑 **VERCEL_TOKEN**
```
Nome: VERCEL_TOKEN
Valor: 7ZMIBKByJ9spuq0n0BbJZure
```
**Descrição:** Token CLI da Vercel para deploy automático

#### 🆔 **VERCEL_ORG_ID**  
```
Nome: VERCEL_ORG_ID
Valor: team_hoUnmVPTyA4DvBbZO0z7IJEF
```
**Descrição:** ID da organização/team Vercel

#### 📦 **VERCEL_PROJECT_ID**
```
Nome: VERCEL_PROJECT_ID  
Valor: prj_1mKxQIx39H0X5ziqL6g8tPxvDMfK
```
**Descrição:** ID específico do projeto Vercel

#### 🔐 **GITHUB_TOKEN** (Opcional - já existe por padrão)
```
Nome: GITHUB_TOKEN
Valor: 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
```
**Nota:** O GitHub Actions já fornece um GITHUB_TOKEN automático. Use apenas se precisar de permissões especiais.

---

## 📸 **SCREENSHOTS DO PROCESSO**

### **Localização dos Secrets:**
```
Repositório → Settings → Secrets and variables → Actions → New repository secret
```

### **Como Adicionar:**
1. Clique em **"New repository secret"**
2. Digite o **Nome** exato (sem espaços)
3. Cole o **Valor** da credencial
4. Clique em **"Add secret"**
5. Repita para todas as 3 secrets obrigatórias

---

## ✅ **VALIDAÇÃO DA CONFIGURAÇÃO**

Após adicionar todas as secrets, você deve ver:

```
✅ VERCEL_TOKEN (Updated X minutes ago)
✅ VERCEL_ORG_ID (Updated X minutes ago)  
✅ VERCEL_PROJECT_ID (Updated X minutes ago)
```

### **Teste de Funcionamento:**
1. Faça um commit no branch `main`
2. Vá em **Actions** no repositório
3. Observe o workflow **"Deploy to Vercel"** executando
4. Se tudo estiver correto, o deploy será concluído com sucesso

---

## 🔧 **CONFIGURAÇÃO DO PROJETO VERCEL**

### **Vinculação Automática:**
O GitHub Actions irá automaticamente:
- Conectar ao seu projeto Vercel usando as credenciais
- Fazer build do código
- Realizar deploy para produção
- Atualizar o domínio live

### **Estrutura Esperada do Projeto:**
```
seu-repositorio/
├── .github/
│   └── workflows/
│       └── deploy.yml     ← Workflow já criado
├── .env.example           ← Variáveis de exemplo
├── package.json          ← Dependências do projeto
├── next.config.js        ← Configuração Next.js
└── src/                  ← Código fonte
```

---

## 🚨 **PROBLEMAS COMUNS**

### **❌ Erro: "VERCEL_TOKEN is not set"**
**Solução:** Verifique se o nome da secret está exato: `VERCEL_TOKEN`

### **❌ Erro: "Project not found"**
**Solução:** Confirme o `VERCEL_PROJECT_ID` nas configurações do Vercel

### **❌ Erro: "Insufficient permissions"**
**Solução:** Verifique se o token Vercel tem permissões de deploy

### **❌ Erro: "Team access denied"**
**Solução:** Confirme se o `VERCEL_ORG_ID` está correto

---

## 📞 **SUPORTE**

### **Logs de Debug:**
- Acesse **Actions** → último workflow → clique no job que falhou
- Examine os logs detalhados para identificar o problema

### **Vercel Dashboard:**
- Acesse [vercel.com/balcks-projects-6ebccbc3](https://vercel.com/balcks-projects-6ebccbc3)
- Verifique os deployments na seção **Deployments**

### **Reexecução de Deploy:**
- Vá em **Actions** → selecione o workflow falhado
- Clique em **"Re-run all jobs"**

---

## 🎊 **CONCLUSÃO**

Após seguir estes passos:

✅ **Deploy automático ativo** a cada push para `main`  
✅ **Secrets configuradas** com segurança no GitHub  
✅ **Projeto linkado** automaticamente ao Vercel  
✅ **URL live** atualizada automaticamente  

**🚀 Seu VisaNetPay está pronto para deploy automático!**

---

**📅 Criado em:** 21/08/2025  
**⚡ Última atualização:** Deploy automático configurado  
**🔐 Status:** Todas as credenciais validadas e aprovadas