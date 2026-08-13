# Sistema de Etiquetas — Condor

Sistema web para geração e registro de etiquetas de recebimento, integrado ao Google Sheets via Google Apps Script e hospedado no GitHub Pages.

## Arquivos do projeto

```
/
├── index.html      → estrutura da página
├── style.css       → estilos (preto / branco / cinza)
├── script.js       → lógica: busca de produto, etiqueta em tempo real, salvar, copiar, imprimir
├── Code.gs         → backend Google Apps Script
└── README.md       → este manual
```

---

## 1. Configurar a planilha do Google Sheets

Use a planilha já existente:
https://docs.google.com/spreadsheets/d/12kHIExQJrFFQo0jYYFlL0Hi1lho2ux9LImMwkM6dDYE/edit

Ela precisa ter **duas abas**:

### Aba "BANCO DE DADOS"
Linha 1 = cabeçalho. A partir da linha 2:

| Código do produto | Descrição        |
|--------------------|-------------------|
| 123456              | Produto exemplo  |
| 789012              | Outro produto    |

### Aba "ETIQUETAS"
Linha 1 = cabeçalho. O sistema preenche a partir da linha 2 automaticamente:

| Código | Descrição | Quantidade | Lote | Data de validade | Nota fiscal | Data do recebimento |
|--------|-----------|------------|------|-------------------|-------------|----------------------|

> Se os nomes reais das suas abas forem diferentes, você **não precisa renomeá-las** — basta ajustar as duas constantes no topo do `Code.gs`:
> ```javascript
> const ABA_BANCO = "BANCO DE DADOS";
> const ABA_ETIQUETAS = "ETIQUETAS";
> ```

---

## 2. Configurar o Google Apps Script

1. Na planilha, vá em **Extensões → Apps Script**.
2. Apague qualquer código de exemplo no arquivo `Code.gs` que abrir.
3. Copie **todo** o conteúdo do arquivo `Code.gs` deste projeto e cole lá.
4. Clique em **Salvar** (ícone de disquete).
5. Se os nomes das suas abas forem diferentes de `BANCO DE DADOS` e `ETIQUETAS`, edite as duas constantes no topo do arquivo.

## 3. Publicar como Web App

1. No editor do Apps Script, clique em **Implantar → Nova implantação**.
2. Em "Selecionar tipo", clique no ícone de engrenagem e escolha **App da Web**.
3. Configure:
   - **Executar como:** Eu (seu e-mail)
   - **Quem pode acessar:** Qualquer pessoa
4. Clique em **Implantar**.
5. O Google vai pedir para você **autorizar o acesso** — siga o fluxo (pode aparecer um aviso de "app não verificado"; clique em Avançado → Acessar [nome do projeto], pois o app é seu).
6. Copie a **URL do Web App** gerada (algo como `https://script.google.com/macros/s/AAAA.../exec`).

> **Importante:** toda vez que você editar o `Code.gs` depois de já ter implantado, é preciso criar **Gerenciar implantações → editar (lápis) → Nova versão → Implantar** para que as mudanças entrem em vigor na mesma URL.

## 4. Colocar a URL no projeto

Abra o arquivo `script.js` e encontre esta linha no topo:

```javascript
const GOOGLE_APPS_SCRIPT_URL = "COLE_AQUI_A_URL_DO_WEB_APP";
```

Substitua pelo link copiado no passo anterior, por exemplo:

```javascript
const GOOGLE_APPS_SCRIPT_URL = "https://script.google.com/macros/s/AAAA.../exec";
```

Salve o arquivo.

---

## 5. Criar o repositório no GitHub

1. Acesse [github.com](https://github.com) e clique em **New repository**.
2. Dê um nome, por exemplo `etiquetas-condor`.
3. Deixe como **público** (necessário para o GitHub Pages gratuito) e crie o repositório vazio (sem README).

## 6. Enviar os arquivos

**Opção A — pela interface do GitHub (mais simples):**
1. No repositório recém-criado, clique em **Add file → Upload files**.
2. Arraste os 4 arquivos: `index.html`, `style.css`, `script.js`, `README.md`.
3. Clique em **Commit changes**.

**Opção B — pelo terminal (git):**
```bash
git init
git add index.html style.css script.js README.md
git commit -m "Sistema de etiquetas Condor"
git branch -M main
git remote add origin https://github.com/SEU-USUARIO/etiquetas-condor.git
git push -u origin main
```

## 7. Ativar o GitHub Pages

1. No repositório, vá em **Settings → Pages**.
2. Em "Source", selecione a branch **main** e a pasta **/ (root)**.
3. Clique em **Save**.
4. Aguarde 1–2 minutos. O GitHub mostrará o endereço do site, algo como:
   `https://seu-usuario.github.io/etiquetas-condor/`

---

## 8. Testar o sistema

1. Abra o link do GitHub Pages.
2. No topo da página, o indicador deve mudar de "Carregando banco de dados…" para "Banco carregado (N produtos)".
3. Digite um código que exista na aba "BANCO DE DADOS" — a descrição deve preencher sozinha.
4. Preencha quantidade, lote, validade e nota fiscal.
5. Confira a etiqueta atualizando em tempo real à direita (ou abaixo, no celular).
6. Clique em **COPIAR** e cole (Ctrl+V) em outro programa para conferir a imagem.
7. Clique em **IMPRIMIR** para testar a impressão só da etiqueta.
8. Clique em **SALVAR ETIQUETA** e confira se uma nova linha apareceu na aba "ETIQUETAS" da planilha.

---

## 9. Solução de problemas comuns

### "Falha ao carregar banco de dados" / erro de CORS
- Confirme que a implantação foi feita como **App da Web**, com **"Executar como: Eu"** e **"Quem pode acessar: Qualquer pessoa"**.
- Confirme que a `GOOGLE_APPS_SCRIPT_URL` no `script.js` termina em `/exec` (não `/dev`).
- Se você editou o `Code.gs` depois de implantar, lembre-se de criar uma **nova versão** da implantação (passo 3, aviso acima) — a URL `/exec` só reflete a última versão implantada.

### "Produto não encontrado" mesmo digitando um código que existe
- Verifique se não há espaços extras na coluna de código na planilha.
- Confirme que o nome da aba bate exatamente com a constante `ABA_BANCO` no `Code.gs`.
- Confirme que os dados começam na **linha 2** (linha 1 é o cabeçalho).

### Erro ao salvar / "Não foi possível salvar"
- Verifique se a aba `ETIQUETAS` existe e o nome bate com a constante `ABA_ETIQUETAS`.
- Abra o link da `GOOGLE_APPS_SCRIPT_URL` diretamente no navegador com `?action=listarProdutos` no final — se aparecer um JSON com os produtos, o backend está funcionando; se der erro, revise a implantação.

### O botão "COPIAR" não cola a imagem
- A API de área de transferência para imagens (`ClipboardItem`) funciona em **HTTPS** (GitHub Pages já é HTTPS) nos navegadores modernos baseados em Chromium (Chrome, Edge) e no Firefox recente. Em navegadores muito antigos ou em alguns celulares, o navegador pode não suportar — nesse caso, o sistema baixa a imagem PNG automaticamente como alternativa.
- No WhatsApp Web, cole a imagem dentro do campo de digitação da conversa (Ctrl+V) — nem todas as versões aceitam colar imagens diretamente.

### A permissão do Google pede autorização toda vez
- Isso só acontece uma vez por implantação/autor. Se continuar pedindo, confirme se você está sempre acessando a mesma URL `/exec` publicada.

---

## Segurança

- Nenhuma senha, chave privada ou credencial do Google fica no HTML/JavaScript público.
- Toda comunicação com a planilha passa pelo Google Apps Script, que atua como intermediário autorizado.
- A única informação pública no código é a URL do Web App, que só executa exatamente as duas ações programadas (`listarProdutos` e `salvarEtiqueta`).
