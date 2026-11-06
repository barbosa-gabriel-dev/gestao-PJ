# 💼 Sistema de Gestão Financeira PJ (Sheets + GitHub Pages)

Sistema web leve, responsivo e **100% gratuito (Serverless)** para controle financeiro empresarial (PJ). Desenvolvido com arquitetura **White-Label (Zero Hardcoding)**, permitindo que o mesmo código-fonte seja conectado a qualquer conta Google em menos de 5 minutos, sem necessidade de editar variáveis no código.

---

## 🏗️ Arquitetura do Sistema

O projeto opera em modelo desacoplado de três camadas a custo zero:

1. **Banco de Dados (Google Sheets):** Armazena os lançamentos, matriz de custos fixos, plano de contas (DRE) e configurações em abas estruturadas.
2. **Backend / API (Google Apps Script):** Script vinculado à planilha que atua como API REST (`doGet` e `doPost`), processando regras de negócio, travas de concorrência (`LockService`) e autenticação via Token/PIN.
3. **Frontend (GitHub Pages):** Single-Page Application (SPA) em arquivo único (`index.html`), utilizando **Tailwind CSS**, **Chart.js** e **FontAwesome**. Armazena as credenciais de conexão localmente no navegador (`localStorage`).

---

## ✨ Funcionalidades Principais

* **Auto-Setup da Planilha (`setupInicialPJ`):** Cria e formata automaticamente todas as abas, cabeçalhos, cores, formatos de moeda/data e categorias iniciais com apenas 1 clique.
* **Dashboard & Fluxo de Caixa em Tempo Real:**
  * KPIs de Entradas Realizadas, A Receber, Saídas Pagas, A Pagar, Saldo Real em Caixa e Saldo Projetado do Mês.
  * Contador automático de pendências vencidas.
  * Gráficos dinâmicos de *Realizado vs. Previsto* e *Despesas por Categoria*.
* **Gestão de Contas a Pagar e Receber (CRUD Completo):**
  * Adição, edição completa, baixa rápida com 1 clique e exclusão de lançamentos.
  * Filtro mensal e filtros combinados por Tipo (Entrada/Saída) e Status (Pendente/Pago).
* **Abatimento / Adiantamento em Parcelas:**
  * Modal visual com simulação em tempo real para descontar pagamentos adiantados (ex: manutenções, taxas ou adiantamentos pagos por fora) diretamente do saldo de uma mensalidade pendente, registrando a saída de caixa imediata e documentando o abatimento no histórico da parcela.
* **Matriz de Custos Fixos Operacionais:**
  * Cadastro, edição, ativação/pausa e exclusão de despesas recorrentes (DAS, internet, aluguel, sistemas).
  * Botão **"Gerar Custos Fixos do Mês"** que lança todas as contas ativas no mês selecionado com proteção contra duplicidade.
* **Plano de Categorias & DRE:**
  * Gerenciamento de categorias pelo próprio painel web com atualização automática em cascata nos lançamentos antigos caso uma categoria seja renomeada.
* **Proteção Anti-Duplicidade (Debouncing + LockService):**
  * Bloqueio imediato dos botões no primeiro clique/toque no celular ou PC e fila de gravação no backend.

---

## 📊 Estrutura do Banco de Dados (Abas da Planilha)

| Aba | Colunas | Descrição |
| :--- | :--- | :--- |
| **`Lancamentos`** | `ID`, `Data_Competencia`, `Data_Vencimento`, `Data_Pagamento`, `Tipo`, `Categoria`, `Centro_Custo`, `Descricao`, `Valor`, `Status`, `Forma_Pagto`, `Observacoes`, `Criado_Em` | Tabela fato principal do fluxo de caixa e contas a pagar/receber. |
| **`Custos_Op`** | `ID_Custo`, `Descricao`, `Categoria`, `Centro_Custo`, `Tipo_Custo`, `Periodicidade`, `Valor_Base`, `Dia_Vencimento`, `Ativo` | Matriz de despesas recorrentes para geração automática mensal. |
| **`Categorias`** | `ID_Cat`, `Nome_Categoria`, `Tipo_Fluxo`, `Classificacao_DRE` | Plano de contas para padronização de lançamentos e estrutura DRE. |
| **`Config`** | `Chave`, `Valor`, `Descricao` | Parâmetros globais (`API_TOKEN`, `NOME_EMPRESA`, `META_FATURAMENTO`). |

---

## 🚀 Guia de Implantação Rápida (Em Qualquer Conta Google)

### Passo 1: Configurar a Planilha e o Backend (Google Sheets)
1. Acesse o [Google Sheets](https://sheets.google.com) na conta desejada e crie uma **Planilha em branco**.
2. No menu superior, clique em **Extensões** > **Apps Script**.
3. Apague o código padrão, cole todo o conteúdo do arquivo `Codigo.gs` e clique no ícone de **Salvar (Disquete)**.
4. Na barra superior do Apps Script, selecione a função **`setupInicialPJ`** e clique em **Executar**.
5. Conceda as permissões da conta Google (*Revisar permissões > Avançado > Acessar projeto > Permitir*).
6. Volte para a planilha: verifique que as 4 abas foram criadas. Na aba **`Config`**, altere o valor da chave **`API_TOKEN`** (padrão `123456`) para um PIN forte de sua preferência e defina o **`NOME_EMPRESA`**.

### Passo 2: Publicar a API (Web App)
1. No editor do **Apps Script**, clique em **Implantar** (canto superior direito) > **Nova implantação**.
2. Clique no ícone de engrenagem e selecione **App da Web**.
3. Configure os campos:
   * **Descrição:** `API Gestão PJ v1`
   * **Executar como:** `Eu`
   * **Quem pode acessar:** `Qualquer pessoa` *(necessário para requisições CORS a partir do GitHub Pages; a segurança é garantida pelo `API_TOKEN`)*.
4. Clique em **Implantar** e copie a **URL do app da Web** gerada (terminada em `/exec`).

> ⚠️ **Importante ao atualizar o código no futuro:** Sempre que alterar o código no Apps Script, vá em **Implantar > Gerenciar implantações > Editar (ícone de lápis) > Versão: "Nova versão" > Implantar**. Isso mantém a mesma URL ativa.

### Passo 3: Publicar o Frontend no GitHub Pages
1. Crie um repositório no GitHub e suba o arquivo **`index.html`** na raiz do projeto.
2. No repositório, acesse **Settings** > **Pages**.
3. Em **Build and deployment** > **Branch**, selecione `main` (pasta `/root`) e clique em **Save**.
4. Aguarde 1 minuto e acesse o link gerado pelo GitHub Pages.

### Passo 4: Conectar o Painel à Planilha
1. Ao abrir o site pela primeira vez (ou clicando no ícone de **Engrenagem ⚙️** no topo), o modal de configuração será exibido.
2. Cole a **URL do Web App** e digite o seu **Token / PIN**.
3. Clique em **Salvar e Conectar**. Os dados ficarão salvos no `localStorage` do seu dispositivo.

---

## 📱 Dica de Uso no Celular (Atalho tipo App)
* **Android (Chrome):** Abra o site do seu GitHub Pages, toque nos **3 pontos** no canto superior direito e selecione **"Adicionar à tela inicial"**.
* **iOS (Safari):** Abra o site, toque no botão **Compartilhar** na barra inferior e selecione **"Adicionar à Tela de Início"**.

---

## 📁 Estrutura de Arquivos do Repositório

```text
├── index.html   # Aplicação Frontend completa (SPA com Tailwind + Chart.js)
├── Codigo.gs    # Script Backend do Google Apps Script (Auto-Setup + API REST)
└── README.md    # Documentação técnica e guia de implantação White-Label
```
