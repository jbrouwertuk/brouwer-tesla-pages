# tesla-pages — statische host voor Tesla Fleet API

Deze map bevat de inhoud van de GitHub-repo die onder `https://tesla.brouwertuk.nl` moet draaien voor Tesla Fleet API domein-verificatie + OAuth-callback.

## Inhoud

- `index.html` — eenvoudige landingspagina (optioneel)
- `callback.html` — **pagina waar Tesla jou naartoe stuurt na OAuth-login**. Toont de autorisatie-code zodat jij die kunt kopiëren naar de admin-pagina.
- `.well-known/appspecific/com.tesla.3p.public-key.pem` — **publieke sleutel**. Wordt door `genereer_tesla_sleutel.bat` geproduceerd. Tesla-servers halen deze op bij domein-verificatie.
- `CNAME` — maakt `tesla.brouwertuk.nl` het canonical domain bij GitHub Pages.

## Setup GitHub Pages (éénmalig)

1. Maak een nieuwe repo op GitHub aan, bijvoorbeeld `brouwer-tesla-pages` (private of public — maakt niet uit, er staan geen geheimen in).
2. Upload de volledige inhoud van deze map (`tesla-pages/`) naar de root van de repo. Dus: niet de map zelf, maar alles wat erin zit, op de root.
3. GitHub → Settings → Pages:
   - **Source:** Deploy from branch → `main` / `(root)` → Save
   - Wacht tot de build klaar is (zie het groene vinkje onder Actions)
4. GitHub → Settings → Pages → **Custom domain:** `tesla.brouwertuk.nl` → Save
5. **DNS:** bij je DNS-provider voeg een CNAME toe:
   ```
   tesla.brouwertuk.nl    CNAME    <jouw-github-user>.github.io
   ```
   (let op: GEEN https:// erin, alleen de hostname)
6. Terug op GitHub, onder Pages, vink **"Enforce HTTPS"** aan zodra dat beschikbaar is (duurt ~15 min na DNS-propagatie).

## Verifiëren

Open in je browser:
- `https://tesla.brouwertuk.nl/` → moet de landingspagina tonen
- `https://tesla.brouwertuk.nl/callback` → moet "Wachten op redirect vanuit Tesla…" tonen (pagina werkt pas écht als Tesla je met `?code=...` terugstuurt)
- `https://tesla.brouwertuk.nl/.well-known/appspecific/com.tesla.3p.public-key.pem` → moet je publieke sleutel retourneren (begint met `-----BEGIN PUBLIC KEY-----`)

## Hoe de sleutel bijwerken

Als je ooit de Tesla-sleutel moet wisselen:
1. Draai opnieuw `genereer_tesla_sleutel.bat` op de dashboard-PC
2. De nieuwe `com.tesla.3p.public-key.pem` staat dan in deze map klaar
3. Commit + push naar GitHub → GitHub Pages deployt automatisch
4. Draai daarna opnieuw `register_tesla_partner.bat` om de nieuwe sleutel bij Tesla te registreren

## Veiligheid

- De publieke sleutel is **niet geheim** — mag letterlijk publiek. Hij is alleen voor signatuur-verificatie door Tesla.
- De **private** key blijft alleen op de dashboard-PC in `%LOCALAPPDATA%\SyntessDB\tesla_private_key.pem`. Deel die nooit.
- De callback-pagina is puur client-side JS en bevat geen geheimen.
