<p align="center">
  <img width="270" height="270" alt="image" src="https://res.cloudinary.com/dt26mfzpw/image/upload/v1788957793/vitalia-logo-full_khdewh.png" />
</p>

# 🐾 Vitalia

A Vitalia é uma solução digital voltada ao **gerenciamento** **e** **acompanhamento** de animais de estimação, conectando tutores e serviços veterinários em uma única plataforma.

A aplicação permite o gerenciamento de informações dos pets e de seus responsáveis, além do acompanhamento de consultas, histórico de atendimentos e informações relacionadas à **saúde** e ao **bem-estar dos animais**.

---

## 📌 Sobre o Projeto

O **Vitalia** é uma solução IoT desenvolvida para monitoramento preventivo de pets utilizando dispositivos inteligentes conectados a uma API REST em nuvem.

A aplicação foi construída com foco em práticas modernas de:

* DevOps
* Cloud Computing
* Persistência de dados
* APIs RESTful
  
---

# 🚀 Benefícios para o Negócio

O Vitalia busca centralizar e facilitar o gerenciamento das informações relacionadas aos animais de estimação, reduzindo a dependência de registros dispersos e facilitando o acesso dos tutores ao histórico e aos dados de seus pets.

Entre os principais benefícios estão:

* **Centralização das informações:** reúne dados dos tutores, pets, consultas e histórico em uma única plataforma.
* **Acesso facilitado:** permite que o tutor consulte as informações de seus pets de forma prática por meio do aplicativo.
* **Melhor acompanhamento da saúde:** possibilita manter um histórico organizado de consultas e informações relevantes do animal.
* **Integração entre sistemas:** APIs permitem a comunicação entre a aplicação mobile e os serviços responsáveis pelo processamento e armazenamento dos dados.
* **Segurança:** utilização de autenticação e gerenciamento de credenciais para proteger o acesso aos recursos da aplicação.
* **Escalabilidade e disponibilidade:** hospedagem da API em ambiente de Cloud com Microsoft Azure App Service, permitindo maior flexibilidade para execução e evolução da solução.

---

# ☁️ Arquitetura da Solução

<img width="1900" height="1100" alt="image" src="https://res.cloudinary.com/dt26mfzpw/image/upload/v1789350105/vitalia-desenho-arquitetura_bvvlur.png" />

---

# 👨🏻‍💻 Processo de criação

Veja o passo a passo de como o ambiente desta entrega foi criado e publicado no Azure App Service.

Os comandos abaixo são os mesmos utilizados no vídeo de demonstração da Sprint 3 e devem ser seguidos exatamente nesta ordem.

# ⚠️ Pré-requisitos

- [Azure CLI](https://learn.microsoft.com/cli/azure/install-azure-cli) instalado
- .NET 10 SDK instalado
- Conta ativa no Azure
- Todos os comandos abaixo foram executados no **PowerShell** (Windows)

---

## 1. Login no Azure
 
```powershell
az login
az account show --output table
```
 
Confirma que você está autenticado e na assinatura correta antes de criar qualquer recurso.
 
---
 
## 2. Criar o Resource Group
 
```powershell
az group create `
  --name rg-vitalia-challenge-2026 `
  --location canadacentral
```
 
O Resource Group agrupa todos os recursos desta entrega (Plano, Web App e configurações).
Verifique qual location está disponível para sua conta Azure.
 
---
 
## 3. Criar o App Service Plan (Windows, F1 - Free)
 
```powershell
az appservice plan create `
  --name plan-vitalia-challenge-2026 `
  --resource-group rg-vitalia-challenge-2026 `
  --location canadacentral `
  --sku F1
```
 
O plano **F1 (Free)** é a camada gratuita recomendada para testes e desenvolvimento.

Como o comando não usa `--is-linux`, o plano é criado no **Windows**, que é o padrão do App Service.
 
---
 
## 4. Criar o Web App
 
```powershell
az webapp create `
  --name vitalia-app `
  --resource-group rg-vitalia-challenge-2026 `
  --plan plan-vitalia-challenge-2026 `
  --runtime "dotnet:10"
```

## 5. Configurar as variáveis de ambiente (App Settings)
 
```powershell
az webapp config appsettings set `
  --name vitalia-app `
  --resource-group rg-vitalia-challenge-2026 `
  --settings `
    ConnectionStrings__OracleConnection="User Id=<SEU_USUARIO_ORACLE>;Password=<SUA_SENHA_ORACLE>;Data Source=oracle.fiap.com.br:1521/ORCL;" `
    JWT_SECRET="<SEGREDO_DO_BACKEND_JAVA>" `
    ASPNETCORE_ENVIRONMENT="Production"
```

---
 
## 6. Clonar o repositório (início dos testes da solução)
 
```powershell
git clone https://github.com/Grupo-BSL-Challenge-FIAP/vitalia-dotnet-sprint3.git
cd vitalia-dotnet-sprint3
```
 
---
 
## 7. Publicar a aplicação
 
```powershell
dotnet restore
dotnet publish Vitalia.API/Vitalia.API.csproj `
  -c Release `
  -o ./publish
```
 
---
 
## 8. Empacotar e enviar o deploy (ZIP deploy via CLI)
 
```powershell
Compress-Archive -Path .\publish\* -DestinationPath .\deploy.zip -Force
 
az webapp deploy `
  --resource-group rg-vitalia-challenge-2026 `
  --name vitalia-app `
  --src-path deploy.zip `
  --type zip
```
 
O deploy é feito por ZIP gerado a partir do `dotnet publish`.
 
---
 
## 9. Validar o deploy
 
```powershell
curl.exe https://vitalia-app.azurewebsites.net/health
```
 
Swagger da aplicação publicada:
 
```
https://vitalia-app.azurewebsites.net/swagger
```
 
---
 
## 🔎 Diagnóstico (se o /health retornar Unhealthy)
 
Se o endpoint `/health` retornar status diferente de `Healthy`, acompanhe os logs em tempo real enquanto atualiza a página:
 
```powershell
az webapp log tail --name vitalia-app --resource-group rg-vitalia-challenge-2026
```

---
 
## 📦 Recursos criados

| Recurso | Nome | Detalhe |
|---|---|---|
| Resource Group | `rg-vitalia-challenge-2026` | Agrupa todos os recursos abaixo |
| App Service Plan | `plan-vitalia-challenge-2026` | Windows, camada **F1 (Free)** |
| Web App | `vitalia-app` | .NET 10, sem containers |
| Banco de dados | Oracle FIAP (`oracle.fiap.com.br:1521`) | Fora do Azure |
 
Todos os recursos acima foram criados **exclusivamente via Azure CLI** (o Portal foi usado só para conferência visual).
 
---


 
## 🌐 Ambiente publicado
 
| Recurso | URL |
|---|---|
| 🚀 API .NET | https://vitalia-app.azurewebsites.net |
| 📘 Swagger / OpenAPI | https://vitalia-app.azurewebsites.net/swagger/ |
| 🩺 Health Check | https://vitalia-app.azurewebsites.net/health |
 
---
 
## 🧹 Limpeza do ambiente
  
```powershell
az group delete --name rg-vitalia-challenge-2026 --yes --no-wait
```
  
