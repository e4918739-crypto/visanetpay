# 📋 Guia Completo de Integração VisaNetPay - PARTE 2
## Comandos MGX e Troubleshooting Completo

---

## 🛠️ **COMANDOS MGX ÚTEIS**

```bash
# SINCRONIZAÇÃO
mgx sync                     # Sincronizar projeto com plataforma
mgx pull-suggestions        # Baixar sugestões IA mais recentes
mgx push-changes            # Enviar mudanças para análise

# ANÁLISE DE CÓDIGO
mgx analyze --full          # Análise completa do código
mgx analyze --security      # Foco em segurança apenas
mgx analyze --performance   # Foco em performance apenas
mgx lint --ai              # Linting com sugestões IA

# TESTES E QUALIDADE
mgx test --ai-suggestions   # Executar testes com insights IA
mgx test --generate        # Gerar testes automaticamente
mgx coverage --report      # Relatório cobertura de testes

# DEPLOY E AMBIENTE
mgx deploy --env staging    # Deploy para staging
mgx deploy --env prod --verify  # Deploy produção com verificações
mgx rollback --to-stable   # Rollback para versão estável
mgx status --all           # Status todos os ambientes

# DOCUMENTAÇÃO
mgx docs generate          # Gerar documentação automática
mgx docs update           # Atualizar docs existentes
mgx changelog            # Gerar changelog automático

# SEGURANÇA E AUDIT
mgx security-scan --detailed    # Scan segurança detalhado
mgx vulnerabilities --fix      # Corrigir vulnerabilidades automaticamente
mgx compliance --check        # Verificar compliance regulatório

# PERFORMANCE
mgx optimize --bundle      # Otimizar bundle size
mgx optimize --images     # Otimizar imagens automaticamente
mgx benchmark            # Executar benchmarks performance

# COLABORAÇÃO
mgx review --assign       # Atribuir reviewers automaticamente  
mgx merge --smart        # Merge inteligente com verificações
mgx notify --team       # Notificar equipe sobre mudanças
```

### **🎯 Exemplos Práticos de Uso MGX**

#### **Cenário 1: Nova Feature Completa**
```bash
# 1. Criar feature com IA
mgx create feature "Sistema de Relatórios Financeiros"
# MGX pergunta especificações e gera estrutura

# 2. Desenvolvimento local
git checkout -b feature/relatorios-financeiros
# ... desenvolvimento ...

# 3. Análise antes do commit
mgx analyze --pre-commit
# MGX sugere melhorias antes de commitar

# 4. Commit e push
git add .
git commit -m "feat: implementar sistema de relatórios financeiros"
git push origin feature/relatorios-financeiros

# 5. MGX automaticamente faz review e sugere melhorias
```

#### **Cenário 2: Correção de Bug com IA**
```bash
# 1. Reportar bug para IA
mgx debug "Erro no cálculo de juros compostos"
# MGX analisa código e sugere possíveis causas

# 2. Implementar correção sugerida
git checkout -b fix/calculo-juros-compostos

# 3. Validar correção com IA
mgx test --focus "juros compostos"
# MGX executa testes específicos e valida correção

# 4. Deploy seguro
mgx deploy --env staging --auto-test
```

---

## 🛠️ **TROUBLESHOOTING COMUM**

### **🐛 1. Problemas com Git**

#### **"Permission denied (publickey)"**
**Causa**: SSH não configurado corretamente

```bash
# Verificar se chave está carregada
ssh-add -l

# Se vazio, carregar chave
ssh-add ~/.ssh/id_ed25519

# Se chave não existe, gerar nova
ssh-keygen -t ed25519 -C "seu-email@exemplo.com"

# Testar conexão
ssh -T git@github.com
```

#### **"Merge conflicts"**
**Causa**: Mudanças conflitantes entre branches

```bash
# 1. Ver arquivos em conflito
git status

# 2. Abrir arquivos e resolver conflitos manualmente
# Procurar por marcadores:
<<<<<<< HEAD
seu código atual
=======
código conflitante
>>>>>>> nome-da-branch

# 3. Após resolver, marcar como resolvido
git add arquivo-resolvido.js

# 4. Finalizar merge
git commit -m "resolve: conflitos de merge na feature X"
```

#### **"Your branch has diverged"**
**Causa**: Branch local diferente do remoto

```bash
# Opção 1: Fazer merge (mantém histórico)
git fetch origin
git merge origin/main

# Opção 2: Rebase (histórico limpo)
git fetch origin
git rebase origin/main

# Opção 3: Reset (CUIDADO - perde mudanças locais)
git fetch origin
git reset --hard origin/main
```

### **📦 2. Problemas com Node.js/npm**

#### **"Module not found"**
**Causa**: Dependências não instaladas ou cache corrompido

```bash
# Solução 1: Reinstalar dependências
rm -rf node_modules package-lock.json
npm install

# Solução 2: Limpar cache npm
npm cache clean --force
npm install

# Solução 3: Verificar Node.js version
node --version  # Deve ser 18+
nvm use 18     # Se usar nvm
```

#### **"EACCES permission denied"**
**Causa**: Permissões incorretas (Linux/Mac)

```bash
# Solução 1: Usar nvm (recomendado)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
source ~/.bashrc
nvm install 18
nvm use 18

# Solução 2: Corrigir permissões npm
sudo chown -R $(whoami) ~/.npm
sudo chown -R $(whoami) /usr/local/lib/node_modules
```

#### **"Out of memory" durante build**
**Causa**: Bundle muito grande ou memória insuficiente

```bash
# Solução 1: Aumentar limite memória
export NODE_OPTIONS="--max-old-space-size=4096"
npm run build

# Solução 2: Build em produção
NODE_ENV=production npm run build

# Solução 3: Analisar bundle size
npm install --save-dev webpack-bundle-analyzer
npm run build
npx webpack-bundle-analyzer .next/static/chunks/*.js
```

### **⚡ 3. Problemas com Vercel**

#### **"Build failed" - Erro comum**
**Causa**: Erro no processo de build

```bash
# 1. Testar build localmente primeiro
cd shadcn-ui
npm run build

# 2. Se falhar localmente, verificar:
npm ls                    # Dependências instaladas
npm audit                # Vulnerabilidades  
npm run lint             # Erros de código

# 3. Verificar logs Vercel Dashboard
# Procurar por: "Command failed", "Module not found", "Type error"

# 4. Soluções comuns:
npm install              # Instalar dependências faltando
npm run build --verbose  # Build com logs detalhados
```

#### **"Environment variables não funcionam"**
**Causa**: Variáveis mal configuradas ou não prefixadas

```bash
# Para Next.js, variáveis do cliente DEVEM começar com NEXT_PUBLIC_:
NEXT_PUBLIC_API_URL=https://api.exemplo.com     ✅ Correto
API_URL=https://api.exemplo.com                 ❌ Errado (só servidor)

# Verificar no código:
console.log('Cliente:', process.env.NEXT_PUBLIC_API_URL);      ✅ Funciona
console.log('Servidor:', process.env.DATABASE_URL);           ✅ Só no servidor
console.log('Erro:', process.env.DATABASE_URL);               ❌ Undefined no cliente
```

#### **"Deploy travado/stuck"**
**Causa**: Build infinito ou erro não reportado

```bash
# Solução 1: Cancelar e tentar novamente
# Vercel Dashboard → Deployments → Cancel Build

# Solução 2: Commit vazio para forçar novo deploy
git commit --allow-empty -m "trigger redeploy"
git push origin main

# Solução 3: Verificar logs Function
# Vercel Dashboard → Functions → Ver logs de erro
```

### **🚀 4. Problemas com MGX**

#### **"MGX sync failed"**
**Causa**: Conectividade ou autenticação

```bash
# 1. Verificar status autenticação
mgx auth status

# Se expirado:
mgx auth login
# Seguir instruções de login

# 2. Verificar conectividade
ping mgx-api.platform.com

# 3. Forçar sincronização
mgx sync --force --verbose

# 4. Reconfigurar se necessário
mgx config reset
mgx config init
```

#### **"AI suggestions não aparecem"**
**Causa**: Quota esgotada ou serviço indisponível

```bash
# 1. Verificar quota
mgx quota status

# 2. Verificar status serviços
mgx status --services

# 3. Se quota esgotada:
mgx quota upgrade  # Ou esperar reset mensal

# 4. Configurar fallback
mgx config set ai_fallback true
```

#### **"Deploy automático não funciona"**
**Causa**: Webhooks não configurados

```bash
# 1. Verificar webhooks MGX
mgx webhooks list

# 2. Reconfigurar se necessário
mgx webhooks setup --github --vercel

# 3. Testar webhook
mgx webhooks test

# 4. Verificar logs webhook
mgx webhooks logs --recent
```

### **🔧 5. Performance e Otimização**

#### **"Site muito lento"**
**Causas e soluções:**

```bash
# 1. Analisar bundle size
npm run build
ls -la .next/static/chunks/

# Se muito grande (>1MB):
# - Implementar code splitting
# - Lazy loading componentes
# - Remover dependências não usadas

# 2. Verificar imagens
# Usar next/image para todas as imagens
# Formatos modernos: WebP, AVIF

# 3. Verificar Core Web Vitals
# Google PageSpeed: https://pagespeed.web.dev/
# Meta: LCP < 2.5s, FID < 100ms, CLS < 0.1
```

#### **"Memory leaks em desenvolvimento"**
**Causa**: useEffect sem cleanup ou listeners não removidos

```javascript
// ❌ PROBLEMA: Effect sem cleanup
useEffect(() => {
  const interval = setInterval(() => {
    // código...
  }, 1000);
  // Faltou cleanup!
}, []);

// ✅ SOLUÇÃO: Sempre fazer cleanup
useEffect(() => {
  const interval = setInterval(() => {
    // código...
  }, 1000);
  
  return () => clearInterval(interval);  // Cleanup
}, []);

// ❌ PROBLEMA: Event listener sem cleanup  
useEffect(() => {
  window.addEventListener('resize', handleResize);
  // Faltou remover listener!
}, []);

// ✅ SOLUÇÃO: Remover listener
useEffect(() => {
  window.addEventListener('resize', handleResize);
  
  return () => window.removeEventListener('resize', handleResize);
}, []);
```

### **🔒 6. Problemas de Segurança**

#### **"API keys expostas"**
**Problema crítico**: Chaves de API no código fonte

```bash
# ❌ NUNCA fazer:
const API_KEY = "sk-1234567890abcdef";  // Hardcoded no código

# ✅ CORRETO: 
const API_KEY = process.env.API_SECRET_KEY;  // Variável de ambiente

# Verificar se há chaves expostas:
git log --all -S "sk-" --source --all     # Procurar por "sk-" em commits
git log --all -S "key" --source --all     # Procurar por "key"

# Se encontrar chaves expostas:
# 1. Revogar chaves imediatamente no provedor
# 2. Gerar novas chaves  
# 3. Adicionar às variáveis de ambiente
# 4. Nunca fazer force-push para "limpar" - não funciona!
```

#### **"Dependências com vulnerabilidades"**
**Causa**: Pacotes desatualizados com security issues

```bash
# 1. Verificar vulnerabilidades
npm audit

# Exemplo resultado:
# found 3 vulnerabilities (1 moderate, 2 high)
# run `npm audit fix` to fix them

# 2. Corrigir automaticamente (se possível)
npm audit fix

# 3. Verificar se precisa correção manual
npm audit fix --force  # Cuidado - pode quebrar!

# 4. Para vulnerabilidades específicas:
npm ls package-vulnerável
npm update package-vulnerável
```

### **📱 7. Problemas Mobile/Responsivo**

#### **"Layout quebrado no mobile"**
**Causa**: CSS não responsivo ou viewport mal configurado

```javascript
// ✅ Verificar viewport no _document.js ou layout:
<meta name="viewport" content="width=device-width, initial-scale=1" />

// ✅ Usar Tailwind responsive classes:
<div className="w-full md:w-1/2 lg:w-1/3">  // Responsivo
<div className="w-96">                       // ❌ Fixo - pode quebrar

// ✅ Testar em diferentes tamanhos:
// Chrome DevTools → Toggle device toolbar (Ctrl+Shift+M)
// Testar: Mobile (375px), Tablet (768px), Desktop (1024px+)
```

### **🔄 8. Problemas de Cache**

#### **"Mudanças não aparecem em produção"**
**Causa**: Cache agressivo do navegador ou CDN

```bash
# 1. Verificar se build tem hash único
ls .next/static/chunks/
# Deve ter: main-[hash].js, não main.js

# 2. Forçar refresh no navegador
# Ctrl+F5 (Windows/Linux) ou Cmd+Shift+R (Mac)

# 3. Verificar headers cache
curl -I https://seu-app.vercel.app
# Procurar: Cache-Control, ETag, Last-Modified

# 4. Limpar cache Vercel (se necessário)
# Vercel Dashboard → Settings → Clear Cache
```

### **🆘 9. Comandos de Emergência**

#### **"Rollback imediato"**
```bash
# 1. Via Vercel (mais rápido - 30 segundos)
# Dashboard → Deployments → Deploy anterior → Promote to Production

# 2. Via Git (mais controle)
git log --oneline                    # Ver commits
git revert HEAD                      # Reverter último commit
git push origin main                 # Deploy do revert

# 3. Reset completo (CUIDADO - perde dados!)
git reset --hard COMMIT_BOM_HASH
git push --force origin main
```

#### **"Site completamente fora do ar"**
```bash
# 1. Verificar status Vercel
https://vercel-status.com/

# 2. Verificar domínio
nslookup seu-dominio.com
ping seu-dominio.com

# 3. Página manutenção temporária
# Criar index.html simples e fazer deploy manual:
echo "<h1>Site em Manutenção</h1>" > index.html
# Deploy via Vercel CLI ou interface

# 4. Comunicar usuários
# Atualizar redes sociais, status page, etc.
```

---

## 📋 **CHECKLIST DE SEGURANÇA**

### **🔒 Antes de Cada Deploy**

```bash
✅ Verificar não há chaves API hardcoded:
   grep -r "sk-" src/           # Stripe keys
   grep -r "pk_" src/           # Public keys  
   grep -r "secret" src/        # Secrets gerais
   
✅ Audit de dependências:
   npm audit                    # Vulnerabilidades
   npm outdated                 # Atualizações disponíveis
   
✅ Variáveis de ambiente configuradas:
   echo $NEXT_PUBLIC_APP_URL    # URLs corretas
   echo $DATABASE_URL           # Banco configurado
   
✅ Build funcionando:
   npm run build                # Build local OK
   npm run start                # Start local OK
   
✅ Testes passando:
   npm run test                 # Se tiver testes
   npm run lint                 # Linting OK
```

### **🛡️ Configurações de Segurança**

```bash
✅ GitHub:
   - Two-factor authentication habilitado
   - Branch protection rules configuradas
   - Secrets não expostos em logs públicos
   
✅ Vercel:
   - Environment variables configuradas
   - Domains com HTTPS
   - Analytics privacy-focused
   
✅ Código:
   - Validação input usuário
   - Sanitização dados  
   - Rate limiting APIs
   - CORS configurado corretamente
```

---

## 🎓 **RESUMO E PRÓXIMOS PASSOS**

### **🚀 Parabéns! Você configurou:**

1. ✅ **GitHub**: Repositório seguro com SSH
2. ✅ **Vercel**: Deploy automático funcionando  
3. ✅ **Workflow**: Git flow profissional
4. ✅ **Monitoramento**: Dashboards e alertas
5. ✅ **MGX**: IA assistindo desenvolvimento
6. ✅ **Troubleshooting**: Soluções para problemas comuns

### **🎯 Próximas Melhorias Sugeridas**

#### **Curto Prazo (1-2 semanas):**
```
📊 Analytics: Google Analytics 4
🧪 Testes: Jest + Testing Library  
🔒 Segurança: Implementar CSP headers
📱 PWA: Service worker + manifest
```

#### **Médio Prazo (1-2 meses):**
```  
🤖 CI/CD: GitHub Actions completo
📈 Monitoring: Sentry error tracking
🏃‍♂️ Performance: Core Web Vitals 100%
🌍 i18n: Internacionalização multi-idiomas
```

#### **Longo Prazo (3-6 meses):**
```
🧠 AI/ML: Análise preditiva transações
⚡ Edge: Cloudflare Workers integration  
📊 BI: Dashboard analytics avançado
🔐 Compliance: SOC2, PCI DSS
```

### **📚 Recursos de Aprendizado**

#### **Documentação Oficial:**
- **Next.js**: https://nextjs.org/docs
- **Vercel**: https://vercel.com/docs  
- **GitHub**: https://docs.github.com/
- **Git**: https://git-scm.com/docs

#### **Cursos Recomendados:**
- **Git/GitHub**: "Git & GitHub Crash Course" (YouTube)
- **Next.js**: "Next.js Full Course" (freeCodeCamp)
- **Deploy**: "Vercel Deployment Guide" (Vercel Learning)

#### **Comunidades:**
- **Discord**: Next.js, Vercel Community
- **Reddit**: r/webdev, r/nextjs
- **Stack Overflow**: Tag [nextjs], [vercel]

### **🆘 Suporte e Ajuda**

#### **Quando Precisar de Ajuda:**
1. **Consulte este guia** primeiro
2. **Verifique documentação oficial**  
3. **Busque no Google** erro específico
4. **Pergunte comunidades** com código exemplo
5. **Abra issue GitHub** se for bug do projeto

#### **Como Pedir Ajuda Efetivamente:**
```markdown
## Problema
Descrição clara do que não funciona

## Reprodução  
Passos exatos para reproduzir

## Código
```código relevante```

## Erro
```mensagem de erro completa```

## Ambiente
- OS: Windows 10 / macOS / Linux
- Node.js: v18.17.0
- Next.js: v13.5.6
- Browser: Chrome 118
```

---

## 🎉 **CONCLUSÃO**

Você agora tem um **sistema completo de desenvolvimento profissional** configurado para o VisaNetPay! 

**O que você consegue fazer:**
- ✅ Desenvolver features com segurança
- ✅ Deploy automático para produção
- ✅ Monitorar performance e erros  
- ✅ Colaborar com equipe efetivamente
- ✅ Resolver problemas rapidamente

**Lembre-se sempre:**
- 🔄 **Sempre** faça pull antes de começar
- 🌿 **Sempre** use branches para features
- 💾 **Sempre** teste antes de fazer merge
- 📊 **Sempre** monitore após deploy
- 🔒 **Nunca** commite secrets no código

**Boa sorte com seu projeto VisaNetPay!** 🚀

---

*Última atualização: Dezembro 2024*
*Versão do guia: v1.0.0*