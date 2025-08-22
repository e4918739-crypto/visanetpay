# 🌍 DEPLOYMENT VIA GITHUB + VERCEL

## 📋 INSTRUÇÕES GITHUB INTEGRATION:

### **🔧 PREPARAÇÃO:**
1. **Extrair ZIP:** `visanetpay-vercel-deployment.zip`
2. **Criar repositório** no GitHub (público ou privado)
3. **Upload arquivos** do projeto extraído

### **📦 COMANDOS GIT:**
```bash
# Navegar para pasta extraída
cd visanetpay-deployment

# Inicializar Git
git init --initial-branch=main
git add .
git commit -m "VisaNetPay Banking System - Production Ready"

# Conectar ao GitHub (substitua URL)
git remote add origin https://github.com/SEU-USUARIO/visanetpay-banking.git
git push -u origin main
```

### **🌐 CONECTAR VERCEL:**
1. **Acesse:** https://vercel.com/new
2. **Clique:** "Import Git Repository"
3. **Selecione:** Seu repositório VisaNetPay
4. **Configure:**
   - Framework: Vite
   - Build Command: pnpm run build
   - Output Directory: dist

---

**✅ RESULTADO:** Deploy automático a cada push!