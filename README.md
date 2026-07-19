# GeoCampo — App Android

Coleta de dados geoespaciais com GNSS, empacotado como aplicativo Android nativo
(via [Capacitor](https://capacitorjs.com)) a partir do app web GeoCampo.

Projeto de **Jacinto Pereira Santos**, professor do curso de Agronomia da
Universidade Federal do Tocantins (UFT).

---

## 1. Colocar este projeto no GitHub

```bash
cd geocampo-app
git init
git add .
git commit -m "GeoCampo - primeira versão do app Android"
git branch -M main
git remote add origin https://github.com/SEU-USUARIO/geocampo-app.git
git push -u origin main
```

Substitua `SEU-USUARIO` pelo seu usuário do GitHub. Se o repositório ainda não existe,
crie um novo (vazio, sem README) em github.com antes do `git push`.

## 2. Gerar o APK automaticamente

Assim que o `git push` terminar, vá até a aba **Actions** do repositório no GitHub.
O workflow `Build APK Android (GeoCampo)` começa a rodar sozinho (leva uns 3-5 minutos).

Quando o círculo ficar verde:
1. Clique na execução mais recente
2. Role até a seção **Artifacts**, no final da página
3. Baixe `geocampo-debug-apk` (é um .zip contendo o `app-debug.apk`)
4. Transfira o `.apk` para o celular (Google Drive, cabo USB, e-mail — o que for mais fácil)
5. No celular, toque no arquivo para instalar. O Android vai pedir para liberar
   "instalação de fontes desconhecidas" apenas na primeira vez — é normal para
   apps fora da Play Store.

Você pode repetir esse processo a qualquer momento: basta alterar o arquivo
`www/index.html` (ou qualquer outro arquivo do projeto), commitar e dar `git push`
de novo. Um novo APK é gerado automaticamente a cada envio.

Também dá pra disparar o build manualmente sem precisar alterar nada: na aba
**Actions**, clique no workflow e depois em **Run workflow**.

## 3. Testar localmente sem GitHub (opcional)

Se você tiver o Android Studio instalado:

```bash
npm install
npx cap sync android
npx cap open android
```

Isso abre o projeto no Android Studio, de onde dá para rodar direto num emulador
ou celular conectado por USB.

## 4. Publicar na Google Play Store (mais adiante)

O workflow atual gera um **APK de depuração** — perfeito para uso pessoal, para a
turma testar, ou para distribuir informalmente. Para publicar na Play Store é
necessário:

1. Gerar uma **chave de assinatura** própria (`keytool -genkey ...`) e guardá-la
   em local seguro — ela nunca deve ir para o GitHub (por isso já está no
   `.gitignore`).
2. Cadastrar essa chave como *secret* no GitHub (Settings → Secrets and
   variables → Actions) e ajustar o workflow para gerar um **AAB assinado**
   (`./gradlew bundleRelease`) em vez do APK de depuração.
3. Criar uma conta de desenvolvedor Google Play (taxa única) e preencher a
   ficha da loja (descrição, capturas de tela, política de privacidade).

Esse é um passo separado e opcional — o app já funciona perfeitamente instalado
por fora da Play Store (o chamado "sideload"), o que é suficiente para uso em
campo e em sala de aula.

## O que foi ajustado em relação à versão web

- As bibliotecas Leaflet, proj4 e JSZip, que no navegador vinham de um CDN
  (cdnjs), agora estão empacotadas dentro do próprio app (`www/vendor/`) —
  garante que o app abra e funcione mesmo sem nenhuma conexão, do primeiro uso.
- O armazenamento de projetos e feições, que usava uma API exclusiva do
  ambiente de desenvolvimento (Claude Artifacts), foi substituído por
  `localStorage`, padrão de qualquer navegador/WebView — os dados continuam
  salvos localmente no aparelho, sem depender de internet.
- O `AndroidManifest.xml` foi configurado com as permissões de localização
  (GPS), vibração e "manter tela ligada", que o app já usa.
- Um pequeno gatilho aciona o diálogo nativo de permissão de localização do
  Android antes de iniciar o GNSS, usando o plugin oficial
  `@capacitor/geolocation` só para essa finalidade — a leitura de posição em
  si continua usando a API padrão de geolocalização do navegador, sem
  necessidade de alterar a lógica existente do app.

## Estrutura do projeto

```
geocampo-app/
├── www/                    ← o app em si (HTML/CSS/JS)
│   ├── index.html
│   └── vendor/              ← bibliotecas empacotadas localmente
├── android/                 ← projeto Android gerado pelo Capacitor
├── capacitor.config.ts      ← nome do app, ID do pacote, pasta web
├── package.json
└── .github/workflows/       ← automação de build (GitHub Actions)
```

Para alterar qualquer parte visual ou funcional do app, edite
`www/index.html` normalmente — é o mesmo arquivo usado na versão web, só que
agora vivendo dentro do projeto Android.
