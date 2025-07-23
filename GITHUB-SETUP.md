# 🐙 Configuração do Repositório GitHub

## 📋 Passo a Passo para Criar Repositório Remoto

### 1️⃣ **Criar Repositório no GitHub**
1. Acesse: https://github.com
2. Clique em **"New repository"** (botão verde)
3. Configure:
   - **Repository name**: `entrevista-tecnica-aws-farmaceutica`
   - **Description**: `Análise de custos EC2 - Economia R$ 56.085,66 anuais`
   - **Visibility**: Private (recomendado para dados corporativos)
   - **NÃO** marque "Add a README file" (já temos)

### 2️⃣ **Comandos para Conectar ao Remoto**
```bash
# Adicionar repositório remoto
git remote add origin https://github.com/SEU-USUARIO/entrevista-tecnica-aws-farmaceutica.git

# Renomear branch para main (padrão atual do GitHub)
git branch -M main

# Fazer push inicial
git push -u origin main

# Push das outras branches
git push origin development
git push origin feature/cost-optimization  
git push origin feature/aws-automation
```

### 3️⃣ **Verificar Conexão**
```bash
git remote -v
git branch -a
```

## 🔑 **Autenticação GitHub**

### Opção A: Personal Access Token (Recomendado)
1. GitHub → Settings → Developer settings → Personal access tokens
2. Generate new token (classic)
3. Selecionar scopes: `repo`, `workflow`
4. Usar token como senha no git push

### Opção B: SSH Key
```bash
# Gerar chave SSH
ssh-keygen -t ed25519 -C "mateus.paula@abstergo.com"

# Adicionar ao ssh-agent
ssh-add ~/.ssh/id_ed25519

# Adicionar chave pública ao GitHub
cat ~/.ssh/id_ed25519.pub
```

## 📊 **Estrutura Final no GitHub**
```
📦 entrevista-tecnica-aws-farmaceutica/
├── 🌿 4 branches (main, development, 2 features)
├── 📄 6 arquivos principais
├── 💰 Economia documentada: R$ 56.085,66
└── 🏷️ Tags para versionamento
```

## 🎯 **Próximos Passos após Push**
1. ✅ Configurar branch protection (main)
2. ✅ Adicionar colaboradores (equipe DevOps)
3. ✅ Criar Issues para implementação
4. ✅ Setup de Actions (CI/CD) se necessário

---
**ABSTERGO INDUSTRIES** | Setup GitHub | 23/07/2025
