# Mostrador OBD — APK com atualizações pelo GitHub

Funciona como a 7Chaves. Fazes a preparação **uma vez só**. Depois, cada atualização é: substituir `www/index.html` no GitHub → esperar ~10 minutos → no telemóvel, **Ajustes → Atualizações → Verificar atualizações**.

---

## O que está nesta pasta

| Ficheiro | Para quê |
|---|---|
| `www/index.html` | A app (é este o ficheiro que vais trocando nas atualizações) |
| `assets/icon.png` | Ícone da app no telemóvel |
| `capacitor.config.json` | Nome e identificador da app (`pt.bbtecnodev.mostrador`). **Nunca mudes o `appId`**, senão o Android trata-a como outra app |
| `package.json` | Peças que o GitHub instala para gerar o APK (inclui o Bluetooth do Android) |
| `.github/workflows/gerar-apk.yml` | A automação que gera, assina e publica o APK |
| `scripts/notas.js` | Lê a versão e o "O que mudou" da app para as notas da atualização |
| `criar-chave.bat` | Só se quiseres uma chave nova, diferente da 7Chaves |

---

## Passo 1 — Repositório

No GitHub: **+ → New repository** → nome `mostrador_obd` · **Public** · não marques mais nada · **Create repository**.

> Tem de ser **público** para o telemóvel ver e descarregar as atualizações sem palavra-passe. O que fica público é o **código** da app; os teus perfis, viagens e testes ficam só no telemóvel.

## Passo 2 — Carregar os ficheiros

1. Extrai o ZIP no PC.
2. No repositório, carrega em **uploading an existing file**.
3. Arrasta **todo o conteúdo** da pasta (incluindo a pasta `.github`) → **Commit changes**.
   - Se a pasta `.github` não aparecer no Windows: Explorador → **Ver → Mostrar → Itens ocultos**.

> A automação arranca logo e **falha** com "Faltam os segredos". É normal até fazeres o passo 3.

## Passo 3 — A chave de assinatura (usa a mesma da 7Chaves)

O Android só aceita atualizações assinadas **sempre com a mesma chave**. Podes usar a chave que já criaste para a 7Chaves: é o mais simples e é o que se faz normalmente com várias apps do mesmo autor.

No repositório `mostrador_obd`: **Settings → Secrets and variables → Actions → New repository secret**. Cria dois, com os **mesmos valores** que puseste no repositório `garagepro`:

| Name | Secret |
|---|---|
| `ANDROID_KEYSTORE_BASE64` | todo o conteúdo do `garagepro-chave-base64.txt` (Bloco de Notas, Ctrl+A, Ctrl+C) |
| `ANDROID_KEYSTORE_PASSWORD` | a palavra-passe dessa chave |

> Os segredos do `garagepro` não se copiam sozinhos: o GitHub não deixa ver nem passar segredos entre repositórios. Precisas do ficheiro `garagepro-chave-base64.txt` e da palavra-passe que guardaste.
>
> Se perdeste o `.txt` mas tens o `garagepro.jks`: abre a PowerShell na pasta dele e corre
> `[Convert]::ToBase64String([IO.File]::ReadAllBytes('garagepro.jks')) | Set-Content -NoNewline garagepro-chave-base64.txt`
>
> Se preferires uma chave só para esta app: corre o `criar-chave.bat` (fora do repositório) e usa os ficheiros `mostrador...` que ele cria.

## Passo 4 — Gerar o primeiro APK

1. Separador **Actions** → à esquerda **Gerar APK** → **Run workflow** → **Run workflow**.
2. Espera ~8–12 minutos até ficar com ✅ verde.
   - Se ficar ❌ vermelho: abre-o, carrega no passo que falhou, copia as últimas linhas e manda-mas.
3. Na página principal do repositório, à direita, aparece **Releases → Mostrador OBD v0.1 (compilação N)**.

## Passo 5 — Instalar no telemóvel

1. No telemóvel abre `https://github.com/Bernas717/mostrador_obd/releases/latest`.
2. Toca no ficheiro `Mostrador-OBD-v….apk` → descarrega → abre.
3. Permite instalar apps desta origem (Chrome) → **Instalar**. Se aparecer o aviso do Play Protect → **Instalar mesmo assim**.
4. Abre a app e toca em **Ligar**. Na primeira vez o Android pede **Dispositivos próximos** e **Localização**: permite as duas. A Localização tem de estar ligada para o Android mostrar os aparelhos Bluetooth.
5. Fecha a app OBD Home antes de ligar: o V011 só aceita uma ligação de cada vez.

---

## Como mandar uma atualização (sempre igual)

1. Recebes de mim o `index.html` novo (ou eu ponho-o diretamente no repositório).
2. No GitHub, abre a pasta `www` → **Add file → Upload files** → arrasta o `index.html` novo → **Commit changes**.
3. O GitHub gera sozinho o APK novo (~10 min, vês em **Actions**).
4. No telemóvel: **Ajustes → Atualizações → Verificar atualizações → Descarregar e instalar** → **Atualizar**.
   - Com a opção "Verificar ao abrir a app" ligada, aparece uma faixa e um ponto laranja em **Ajustes** quando houver versão nova.

Os perfis, os painéis, as viagens e os testes mantêm-se nas atualizações.

**Versões:** cada envio gera uma compilação nova (o número sobe sempre). A versão começa em 0.1 e sobe 0.1 por cada dia com alterações (todas as correções do mesmo dia ficam na mesma versão).

## Problemas comuns

- **"App não instalada" ao atualizar** → o APK foi assinado com outra chave. Confirma os segredos do passo 3.
- **"Ainda não há nenhuma versão publicada"** → a automação ainda não acabou ou falhou (vê em **Actions**).
- **"O GitHub limitou os pedidos"** → verificaste muitas vezes seguidas; espera uns minutos.
- **A lista de aparelhos aparece vazia** → liga a Localização e o Bluetooth, confirma que o V011 está na ficha e a ignição ligada, e que a OBD Home está fechada.
