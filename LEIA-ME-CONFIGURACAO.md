# 🔧 INSTRUÇÕES DE CONFIGURAÇÃO
## Amanda Reis Advocacia — Blog + Painel Admin

---

## VISÃO GERAL

A nova estrutura funciona assim:
- **Blog** lê artigos diretamente do **Firebase Firestore** (sem precisar subir arquivo no GitHub)
- **Fotos** sobem automaticamente para o **Cloudinary** ao fazer upload no painel
- **Artigos programados** ficam invisíveis no site até a data definida
- **Login** do painel usa Firebase Authentication (e-mail + senha)

---

## PASSO 1 — Criar projeto no Firebase

1. Acesse https://console.firebase.google.com
2. Clique em **"Adicionar projeto"** → dê um nome (ex: `amanda-reis-adv`)
3. Pode desativar o Google Analytics se quiser
4. Aguarde criar e clique em **"Continuar"**

---

## PASSO 2 — Configurar Firebase Authentication

1. No menu lateral: **Build → Authentication**
2. Clique em **"Get started"**
3. Em **Sign-in method**, ative **"E-mail/senha"**
4. Vá em **"Users"** → **"Add user"**
5. Cadastre o e-mail e senha da Amanda (ex: `amanda@draamandareis.com.br`)
6. Guarde bem esses dados — serão usados para login no painel

---

## PASSO 3 — Configurar Firestore Database

1. No menu lateral: **Build → Firestore Database**
2. Clique em **"Create database"**
3. Escolha **"Start in production mode"** → Avançar
4. Escolha a região **`southamerica-east1`** (São Paulo) → Habilitar
5. Vá em **"Rules"** e substitua o conteúdo por:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Leitura pública dos artigos (site pode ler)
    match /artigos/{docId} {
      allow read: if true;
      allow write: if request.auth != null;
    }
  }
}
```

6. Clique em **"Publish"**

---

## PASSO 4 — Pegar as credenciais do Firebase

1. No menu lateral: clique na **engrenagem ⚙️ → "Project settings"**
2. Role até **"Your apps"** → clique em **"Web"** (ícone `</>`)
3. Dê um apelido (ex: `blog-amanda`) e clique em **"Register app"**
4. Vai aparecer um bloco `firebaseConfig` com esses campos:

```js
const firebaseConfig = {
  apiKey: "AIza...",
  authDomain: "amanda-reis-adv.firebaseapp.com",
  projectId: "amanda-reis-adv",
  storageBucket: "amanda-reis-adv.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123"
};
```

5. Copie esses valores

---

## PASSO 5 — Configurar Cloudinary

1. Acesse https://cloudinary.com e crie uma conta gratuita
2. No Dashboard, anote o seu **Cloud Name** (ex: `amandareisadv`)
3. Vá em **Settings → Upload → Upload presets**
4. Clique em **"Add upload preset"**
5. Configure:
   - **Preset name**: `blog_amanda` (guarde esse nome)
   - **Signing Mode**: `Unsigned` ← IMPORTANTE
   - **Folder**: `blog` (opcional, para organizar)
6. Clique em **Save**

---

## PASSO 6 — Colar as credenciais nos arquivos

Abra os 4 arquivos abaixo e substitua os placeholders:

### Arquivos a editar:
- `admin/index.html`
- `admin/painel.html`
- `blog/index.html`
- `blog/artigo.html`

### No `admin/painel.html`, configure também o Cloudinary:
```js
const CLOUDINARY_CLOUD  = "amandareisadv";   // seu Cloud Name
const CLOUDINARY_PRESET = "blog_amanda";      // seu Upload Preset
```

### Nos 4 arquivos, substitua o bloco FIREBASE_CONFIG:
```js
const FIREBASE_CONFIG = {
  apiKey:            "COLE_SEU_apiKey_AQUI",        // ← colar aqui
  authDomain:        "COLE_SEU_authDomain_AQUI",    // ← colar aqui
  projectId:         "COLE_SEU_projectId_AQUI",     // ← colar aqui
  storageBucket:     "COLE_SEU_storageBucket_AQUI", // ← colar aqui
  messagingSenderId: "COLE_SEU_messagingSenderId_AQUI", // ← colar aqui
  appId:             "COLE_SEU_appId_AQUI"          // ← colar aqui
};
```

---

## PASSO 7 — Importar os artigos existentes

Os artigos já escritos estão no arquivo `blog/artigos.js` (15 artigos,
sendo 5 publicados e 10 programados para abril/maio/junho de 2025).

Para importá-los no Firestore de uma vez:

1. Acesse o Firebase Console → Firestore Database
2. Clique em **"Start collection"** → nome: `artigos`
3. Ou use o painel admin para adicionar/editar artigos manualmente

**ATENÇÃO:** Após configurar Firebase, o site lerá do Firestore.
O arquivo `blog/artigos.js` é um backup e pode ser ignorado.

---

## PASSO 8 — Subir no Netlify/GitHub

Suba os arquivos normalmente. A estrutura de pastas é:

```
/
├── index.html          (homepage principal — não modificada)
├── style.css
├── images/
├── blog/
│   ├── index.html      ← NOVO (lê do Firebase)
│   ├── artigo.html     ← NOVO (lê do Firebase por ID)
│   └── artigos.js      ← backup dos artigos iniciais
└── admin/
    ├── index.html      ← login Firebase Auth
    └── painel.html     ← painel completo Firebase + Cloudinary
```

---

## COMO USAR O PAINEL

1. Acesse `/admin/` no seu site
2. Faça login com o e-mail e senha cadastrados no Firebase Auth
3. Clique em **"Novo artigo"**
4. Preencha título, categoria, data, resumo e conteúdo
5. Faça upload da foto (vai direto pro Cloudinary, sem precisar de nada)
6. Clique em **"Salvar no Firebase"**

Se a data escolhida for **hoje ou anterior** → artigo aparece imediatamente no site.
Se a data for **no futuro** → artigo fica programado e aparece com badge laranja no painel.

---

## DÚVIDAS FREQUENTES

**"Artigo não aparece no site"**
→ Verifique se a data de publicação já chegou. No painel, artigos programados
aparecem com badge laranja "Programado".

**"Foto não sobe"**
→ Verifique se o Upload Preset está como `Unsigned` no Cloudinary.
Verifique também se o Cloud Name e o Preset estão corretos no `painel.html`.

**"Erro de permissão no Firestore"**
→ Verifique se as Rules do Firestore estão configuradas como no Passo 3.

---

*Gerado automaticamente — Amanda Reis Advocacia 2025*
