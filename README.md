# Formulacija — besplatna arhiva video linkova

Sajt Formule 1 sa sezonama, pretragom, vrstama snimaka i linkovima ka profilima Formulacija. Video snimci ostaju na drugim servisima. Sajt nema video skladište, plaćeni server ni zahtev za karticom.

## Pokretanje

Node.js 22+ (preporučeno 24 iz `.nvmrc`). Nema npm zavisnosti.

```sh
cd /workspace/Web-sajt
npm test
npm run build
npm start
```

Server koristi `public/` na portu 3000. `PORT` menja port. `npm run preview` služi `dist/`. Ne pokreći oba na istom portu. Okruženje zadatka je već izolovano; koristi postojeći checkout, bez dodatnog worktree-a osim na izričit zahtev korisnika.

## Dodavanje trke

Otvori `/urednik.html` na sajtu:

1. Dodaj sezonu ako nedostaje.
2. Unesi naziv, stazu, sezonu, vrstu snimka i HTTPS video link.
3. „Proveri plejer“ otvara probni prikaz. „Dodaj link u nacrt“ dodaje unos.
4. Objavi nacrt jednim od načina ispod.

Nacrt se automatski čuva u lokalnom skladištu tog pregledača. **Nacrt nije javna izmena sajta.** Preuzmi `content.json` kao rezervnu kopiju. Možeš ga vratiti kroz „Učitaj JSON nacrt“. Dugme Izmeni menja postojeći unos; Ukloni uklanja link iz nacrta, bez brisanja videa na izvornom servisu.

### Objavljivanje bez tokena

Klikni „Preuzmi nacrt“. Prijavi se na GitHub, otvori https://github.com/conevidanovic55-ui/Web-sajt/upload/main/public , izaberi preuzeti `content.json` i klikni **Commit changes**. Datoteka mora ostati nazvana `content.json`, u direktorijumu `public`. GitHub Actions zatim objavljuje novu verziju sajta. Posle uspešnog procesa promene postaju javne.

### Objavljivanje iz urednika

Opciono napravi fine-grained GitHub token na https://github.com/settings/personal-access-tokens/new :

- Resource owner: `conevidanovic55-ui`.
- Repository access: samo `Web-sajt`.
- Repository permission: **Contents — Read and write** (Metadata read dolazi automatski).
- Postavi kratak rok važenja i obnovi ga kada istekne.

Unesi token isključivo u polje urednika na sopstvenom sajtu. Nemoj ga slati u čet. Klikni „Poveži nalog i učitaj arhivu“ pre uređivanja; učitava se najnovija verzija sa GitHub-a. Token ostaje samo u memoriji kartice: ne čuva se u localStorage-u, JSON-u, repozitorijumu ni kolačićima. Zatvaranje/ponovno učitavanje kartice ga uklanja. „Objavi na sajtu“ pravi izmenu datoteke `public/content.json` na grani main. GitHub Pages objavljuje izmenu nakon uspešnog workflow-a, obično za nekoliko minuta; potvrda API-ja ne znači da je deployment već završen.

Zaštita SHA verzijom sprečava prepisivanje novijih promena iz druge kartice. Ako se pojavi konflikt, preuzmi nacrt, ponovo poveži nalog i uskladi izmene sa najnovijom arhivom. Postojeći nacrt se ne odbacuje pri povezivanju bez potvrde.

## Podržani servisi

- **OK.ru**: običan video link ili videoembed link. Primer korisnika: `https://ok.ru/video/11719629212384` postaje `https://ok.ru/videoembed/11719629212384`.
- YouTube, Vimeo, Dailymotion, Streamable i Streamtape: prepoznavanje linkova i ugrađeni plejer.
- Rumble: HTTPS `/embed/v…/` link.
- Direktni HTTPS MP4/WebM linkovi: HTML video plejer.
- Drugi HTTPS linkovi: otvaranje originalnog snimka u novoj kartici, bez obećanja da servis podržava ugrađivanje.

Ne unosi iframe HTML kod niti linkove za preuzimanje koji zahtevaju prijavu. Snimak mora biti dostupan gledaocima. Servis može zabraniti ugrađivanje, tražiti prijavu, ograničiti region ili ukloniti video; sajt to ne zaobilazi. U plejeru postoji i link ka izvornom snimku. Spoljni iframe je sandboxovan bez iskačućih prozora i preusmeravanja glavne stranice.

Javna arhiva sadrži korisnikov OK.ru snimak „VN Kine — Sport Klub Hrvatska“, sezona 2025, i tabove za 2025. i 2026. godinu. Početno je izabrana najnovija sezona koja ima video linkove. Testovi koriste zasebnu probnu arhivu sa više sezona. Dekorativne ilustracije nisu zvanične mape staza; hero slika je generisana za projekat. Sajt je nezavisna zajednica, a ne zvaničan sajt Formule 1.

## Besplatno javno objavljivanje

Predviđen je **GitHub Pages za javni repozitorijum na GitHub Free**, bez kartice. Nema kupovine domena: dobija se projektna podadresa oblika:

`https://conevidanovic55-ui.github.io/Web-sajt/`

Ovo je očekivana adresa; aktivna je tek nakon uspešnog objavljivanja i provere. Ako je repozitorijum privatan, nemoj uključivati plaćeni plan: vlasnik prvo treba da odobri javnost repozitorijuma ili izabere drugi besplatan statički hosting. Pages ima standardna ograničenja usluge, dok video saobraćaj ide ka servisu na kojem je snimak.

Za javno objavljivanje izaberi **Settings → Pages → Build and deployment → Source: Deploy from a branch → main → / (root)** i klikni Save. Nije potrebno ručno pokretati workflow niti birati GitHub Actions kao izvor. GitHub automatski objavljuje promene posle svakog push-a na main.

Korenski `index.html` preusmerava u `public/`, a `.nojekyll` čuva statičke fajlove. Direktna putanja sajta je i `https://conevidanovic55-ui.github.io/Web-sajt/public/`. Kontrolni workflow Provera sajta proverava kod, ali ne menja objavljivanje. `dist/` ostaje dostupan za lokalni preview ili drugi statički hosting.

Ne traži se Cloudflare nalog, R2, Cloudflare token niti kartica. Stari ignorisani `.data/` fajlovi prethodne razvojne verzije nisu deo novog sajta i sačuvani su kako korisnički podaci ne bi bili obrisani. Prethodni Cloudflare zahtevi mogu ostati u starom nacrtu podešavanja okruženja; alat ne podržava njihovo brisanje, pa se po potrebi uklanjaju u korisničkom panelu. Aplikacija ih ne koristi.

## Provere

```sh
npm test
npm run build
python3 tests/browser_smoke.py
```

Prve dve komande traže samo Node. Browser test koristi preinstalirani Python Playwright i Chromium. Proverava mobilni prikaz, filtere, projektni `/Web-sajt/` prefiks, OK.ru embed URL i sandbox, spoljne linkove, čuvanje/uvoz/izvoz nacrta i GitHub objavljivanje sa presretnutim API odgovorima. GitHub i video frejmovi u testu su simulirani: test ne potvrđuje stvarnu reprodukciju na OK.ru niti živo javno objavljivanje. Slike prikaza su u ignorisanom `test-results/`.
