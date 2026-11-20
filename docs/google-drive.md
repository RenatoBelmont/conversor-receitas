# Configurar o backup no Google Drive

O botão **Salvar no Drive** (aba Backup) precisa de um **ID do cliente OAuth** do Google.
É gratuito, leva uns 15 minutos e só precisa ser feito uma vez. Depois disso, qualquer
pessoa autorizada (você e o Bruno) usa o Drive direto pelo app.

> Os nomes dos menus podem variar um pouco, porque o Google muda o painel de tempos em tempos.
> Os nomes em inglês aparecem entre parênteses para ajudar quando o painel estiver em inglês.

---

## 1. Criar o projeto

1. Acesse **https://console.cloud.google.com** com a sua conta Google.
2. No seletor de projeto (topo da página), clique em **Novo projeto** (*New project*).
3. Nome: `Conversor de Receitas` → **Criar**.
4. Confira se o projeto novo está selecionado no topo antes de continuar.

## 2. Ativar a API do Google Drive

1. Menu ☰ → **APIs e serviços** → **Biblioteca** (*APIs & Services → Library*).
2. Busque **Google Drive API** → abra → **Ativar** (*Enable*).

## 3. Configurar a tela de consentimento

1. Menu ☰ → **Google Auth Platform** → **Branding**.
   Se aparecer "Google Auth Platform not configured yet", clique em **Começar** (*Get started*).
2. Preencha:
   - **Nome do app:** `Conversor de Receitas`
   - **E-mail de suporte:** o seu e-mail
3. **Público** (*Audience*): escolha **Externo** (*External*).
4. **Informações de contato:** o seu e-mail.
5. Aceite a política de dados de usuário das APIs do Google → **Criar**.

## 4. Autorizar quem vai usar (usuários de teste)

O app fica em **modo de teste**, e só quem estiver nesta lista consegue entrar.

1. **Google Auth Platform** → **Público** (*Audience*).
2. Em **Usuários de teste** (*Test users*) → **Adicionar usuários** (*Add users*).
3. Adicione o seu Gmail e o do Bruno → **Salvar**.

> **Por que não publicar o app?** Em modo de teste, cabem até 100 usuários. Isso é mais do que
> suficiente aqui, e o app não precisa passar pela verificação do Google.

## 5. Declarar a permissão usada

1. **Google Auth Platform** → **Acesso a dados** (*Data Access*).
2. **Adicionar ou remover escopos** (*Add or remove scopes*).
3. Em "Adicionar escopos manualmente", cole:
   ```
   https://www.googleapis.com/auth/drive.file
   ```
   → **Adicionar à tabela** → **Atualizar** → **Salvar**.

Esse escopo é o mais restrito do Drive: **o app só enxerga os arquivos que ele mesmo criou**
(o arquivo de backup). Ele não tem acesso a mais nada do seu Drive. O Google classifica esse escopo como
"não sensível".

## 6. Criar o ID do cliente

1. **Google Auth Platform** → **Clientes** (*Clients*) → **Criar cliente** (*Create client*).
2. **Tipo de aplicativo:** **Aplicativo da Web** (*Web application*).
3. **Nome:** `Conversor de Receitas (GitHub Pages)`.
4. **Origens JavaScript autorizadas** (*Authorized JavaScript origins*) → **Adicionar URI**:
   ```
   https://renatobelmont.github.io
   ```
5. **URIs de redirecionamento autorizados** (*Authorized redirect URIs*) → **Adicionar URI**:
   ```
   https://renatobelmont.github.io/conversor-receitas/
   ```
   ⚠️ Tem que ser **exatamente** assim: `https`, tudo minúsculo e **com a barra no final**.
6. **Criar**. Copie o **ID do cliente** (*Client ID*), que termina em `.apps.googleusercontent.com`.

> O Google também pode mostrar uma **chave secreta do cliente** (*client secret*). **Não use e não
> cole em lugar nenhum.** O app não precisa dela. Já o ID do cliente não é segredo e pode ficar
> no código público.

## 7. Colocar o ID no app

1. No GitHub, abra o repositório → arquivo **`config.js`** → ícone de lápis (✏️ *Edit*).
2. Cole o ID entre as aspas:
   ```js
   window.RECEITAS_CONFIG = {
     googleClientId: '123456789-abcdef.apps.googleusercontent.com'
   };
   ```
3. **Commit changes**. O GitHub Pages publica em 1 a 2 minutos.

## 8. Testar

1. Abra o app → aba **Backup**. O cartão "Google Drive" deve mostrar os botões
   **Salvar no Drive** e **Restaurar**. Se ainda disser "não configurado", feche e abra o app.
2. Toque em **Salvar no Drive** → escolha a conta Google.
3. Como o app está em teste, o Google mostra **"O Google não verificou este app"**.
   Toque em **Continuar**. Isso é esperado, porque você é o desenvolvedor do app.
4. Marque a permissão do Drive, se ela aparecer como caixa de seleção, → **Continuar**.
5. O app volta sozinho para a aba Backup e envia o arquivo.
   No Drive, ele aparece como **`Conversor de Receitas - backup.json`**.

---

## Como funciona no dia a dia

- **Salvar no Drive** cria o arquivo na primeira vez e depois **atualiza o mesmo arquivo**.
  O Drive guarda as versões anteriores: no Drive, clique com o botão direito no arquivo →
  *Informações do arquivo* → *Gerenciar versões*.
- **Restaurar** baixa o backup e mostra a mesma prévia da importação (**Mesclar** ou
  **Substituir tudo**).
- O acesso dura **1 hora**. Depois disso, o app pede para entrar de novo; quando já houve
  autorização, isso costuma ser só um toque.
- **Desconectar do Google** (na aba Backup) apaga o acesso deste aparelho. Para revogar de vez:
  https://myaccount.google.com/permissions → *Conversor de Receitas* → **Remover acesso**.

## Problemas comuns

| Mensagem | Causa e solução |
|---|---|
| `Erro 400: redirect_uri_mismatch` | O endereço do passo 6.5 não bate. Confira a barra final e se é `https`. |
| `Erro 403: access_denied` / "não concluiu o processo de verificação" | A conta usada não está nos **usuários de teste** (passo 4). |
| Botão do Drive diz "não configurado" | O `config.js` não foi salvo, ou o app ainda está com a versão antiga. Feche e abra o app. |
| "Acesso ao Drive não autorizado" | O login foi cancelado ou a permissão do Drive foi desmarcada. Tente de novo e marque a permissão. |
| "Nenhum backup deste app no seu Drive" | Ainda não houve nenhum **Salvar no Drive** com essa conta, ou o arquivo foi para a lixeira. |

## Detalhes técnicos

- O login usa o fluxo OAuth 2.0 para aplicativos da Web do lado do cliente
  (`response_type=token`), por **redirecionamento na mesma janela**. A janela pop-up do Google
  não funciona bem em app instalado (PWA) no Android, por isso ela não é usada.
- Nenhuma biblioteca do Google é carregada. As chamadas vão direto para a API REST do Drive v3:
  `files.list` com `appProperties`, `files.create` *multipart* e `files.update` *media*.
- O token fica no armazenamento local do aparelho, com validade de 1 hora. O parâmetro `state`
  é verificado na volta, para evitar respostas forjadas.
- Para testar no computador (`http://localhost:8000`), acrescente essa origem e
  `http://localhost:8000/` como URI de redirecionamento no mesmo cliente.
