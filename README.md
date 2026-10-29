# Bearly Qualified – Hässelby v10.63

Side-scrolling cykelspel. Körs helt i webbläsaren, ingen build och inga beroenden.

## Spela

* **Online:** GitHub Pages (se nedan).
* **Lokalt:** öppna `index.html` direkt i webbläsaren, eller kör en liten server
  i mappen: `python3 -m http.server 8000` och gå till <http://localhost:8000>.

Styrning: piltangenter eller WASD, mellanslag för hopp, `R` reset, `C` retry,
`N`/`M` björnposer, `V`/`B` babyposer, `F3` debugmeny.

## Filstruktur

| Fil / mapp      | Storlek | Innehåll |
|-----------------|---------|----------|
| `index.html`    | ~8 kB   | Bara markup: header, canvas, HUD, knappar |
| `css/game.css`  | ~41 kB  | All styling |
| `js/assets.js`  | ~39 kB  | Sprite-registret: namn → filnamn i `assets/` |
| `js/game.js`    | ~371 kB | Spelkoden (oförändrad) |
| `assets/`       | ~29 MB  | 775 sprites (png/webp/jpg) |

Tidigare låg allt i en enda `*_PLAY.html` på 47 MB, för att varenda sprite var
inbäddad som base64 i HTML-koden. Base64 gör binärdata ~33 % större och gick
inte att ladda upp till GitHub. Nu ligger bilderna som riktiga filer, laddas
parallellt av webbläsaren och cachas mellan sidladdningar.

Inget i spellogiken är ändrat. Spelet satte redan sina bilder med
`img.src = window.BEARLY_ASSETS[namn]`, så det enda som gjordes var att byta
varje `"data:image/png;base64,…"`-sträng mot `"assets/namn.png"`.

`js/game.js` ligger kvar som en fil med flit: koden är en enda IIFE där allt
delar samma scope, så en uppdelning skulle kräva en riktig refaktorering av
spellogiken, inte bara en filklyvning.

## Lägg upp på GitHub Pages

```bash
git init
git add .
git commit -m "Bearly Qualified v10.63"
git branch -M main
git remote add origin git@github.com:<användarnamn>/<repo>.git
git push -u origin main
```

Sedan: **Settings → Pages → Source: Deploy from a branch → `main` / `/ (root)`**.
Spelet hamnar på `https://<användarnamn>.github.io/<repo>/`.

Största enskilda filen är `assets/introStill.png` på 2,4 MB — långt under
GitHubs gräns på 100 MB per fil, och hela repot är ~30 MB.

## Känd sak sedan tidigare

22 spritenamn som koden genererar i `load()` (`v57_scene_*`, några
`v158_firecracker_scooter_*_3`) har aldrig funnits bland bilderna — de saknades
i 47 MB-versionen också. De ger 404 i konsolen men fångas av `img.onerror` och
påverkar inte spelet.

En sak till om `file://`: spelet går att öppna direkt från disk som förut, men
debug-hjälpfunktionen `window.BEARLY_CAPTURE_METRO()` använder
`canvas.toDataURL()`, vilket Chrome blockerar när bilderna laddas från `file://`
(canvasen blir "tainted"). Kör via en lokal server eller GitHub Pages om du
behöver den. Själva spelet påverkas inte.
