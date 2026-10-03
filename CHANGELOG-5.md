# Changelog

## v0.4.3 – 2026-09-30
- Nenápadná řádka „Licence aplikace: Zdeněk Novotný“: v nastavení AI Workspace pod číslem verze, v BOZP obchůzce (BOZP v1.1) dole na hlavní obrazovce a malým písmem v patičce protokolu; uvedeno i v hlavičce zdrojových souborů.

## v0.4.2 – 2026-09-30
**Přidáno**
- **BOZP jako jeden soubor:** `public/bozp-offline.html` – celá aplikace v jediném HTML souboru (45 kB), funguje bez serveru a bez internetu, stačí ho otevřít v prohlížeči telefonu. Vytváří se příkazem `node scripts/make-bozp-offline.js` z `bozp.html` + `bozp.js` (po úpravě těchto souborů ho vygeneruj znovu).

**Známé omezení**
- Android (Chrome): soubor otevři z „Soubory“ v Chrome; data se ukládají u tohoto souboru (přesunutí/přejmenování souboru může znamenat prázdná data – dělej zálohy). iPhone (Safari) lokální HTML soubory často neotevře nebo neukládá spolehlivě.

## v0.4.1 – 2026-09-30
**Přidáno**
- **BOZP obchůzka** – samostatná stránka `/bozp.html` pro telefon (Android i iPhone), otevře se na stejné adrese jako AI Workspace, např. `http://192.168.x.x:3000/bozp.html`. Nepotřebuje přihlášení ani API, data zůstávají jen v telefonu (IndexedDB), na server se neposílá nic.
  - Hlavička obchůzky se 6 poli (názvy lze změnit), více obchůzek podle dnů.
  - Nález: fotky z fotoaparátu nebo galerie (zmenší se na 1600 px), **žlutá šipka** kreslená prstem (původní fotka se nemění, šipky lze vrátit/smazat), závažnost 1–3 (význam lze upravit), kategorie (lze upravit), místo, popis (diktování přes mikrofon klávesnice).
  - Spolehlivost: uložení se po zápisu ověří, rozepsaný nález se průběžně ukládá jako koncept, záloha/obnova do souboru .json, upozornění když ukládání v telefonu nefunguje.
  - **Protokol:** tisknutelný A4 (Tisk / uložit jako PDF) nebo samostatný HTML soubor; hlavička, souhrn podle kategorií a závažnosti, nálezy od nejzávažnějších s fotkami a šipkami, řádky pro podpisy.

**Neřešeno / známé omezení**
- Stránka se načítá ze serveru, takže při prvním otevření musí PC běžet a telefon být ve stejné Wi-Fi. Po otevření pracuje bez spojení, ale bez nainstalované aplikace ji po zavření nelze otevřít offline (prohlížeč nedovolí offline režim na adrese bez HTTPS).
- Data jsou jen v daném telefonu a prohlížeči; je třeba dělat zálohy.

## v0.4.0 – 2026-09-30
**Přidáno**
- **Dílna (🛠):** z kódu, který ti model napíše v chatu, sestavíš spustitelný **Windows program (.exe)** přímo na svém PC. Tlačítko „Do Dílny“ u každého bloku kódu uloží kód do projektu (cestu souboru lze uvést komentářem `// soubor: lib/pomocne.js`). V Dílně lze soubory upravovat, vybrat hlavní soubor a klepnout na **Sestavit**; hotový .exe se stáhne. Program je samostatný (obsahuje Node.js, ~120 MB), na cílovém PC nic nevyžaduje.
- Sestavení probíhá kroky: kontrola místa → sloučení souborů do jednoho → Node SEA blob → vložení do kopie node.exe (nástroj `postject`, stáhne se při prvním sestavení do `dilna\_tools`, potřebuje internet). Průběh je vidět v logu.
- Nápověda „Jak zadat úkol modelu“, aby generoval kód, který Dílna umí sestavit.

**Bezpečnost**
- Dílna je jen pro správce (`ADMIN_USER` v `.env`, jinak první založený uživatel) a jen z PC, kde server běží (adresa `http://localhost:3000`, ne z Androidu ani přes IP).
- Kód projektu se při sestavování **nespouští**. Kontrola názvů/cest, limity (500 kB/soubor, 200 souborů, 100 projektů), jen jedno sestavení naráz, kontrola volného místa (≥ 400 MB).
- Nově se chyby v asynchronních handlerech nezhroutí server.

**Neřešeno / známé omezení**
- Podporován jen CommonJS (`require`), ne `import/export`, a žádné npm balíčky. Jen Windows .exe.
- Vložení do .exe (postject) bylo vyzkoušeno jen s atrapou, první skutečné sestavení ověří až tvůj PC.
- Program není podepsaný – Windows SmartScreen může ukázat varování („Další informace → Přesto spustit“).
- APK se připravuje na v0.5 (vyžaduje Android SDK, 8–15 GB; ideálně na disk D:).
- Volitelně v `.env`: `ADMIN_USER`, `DILNA_DIR` (umístění projektů, výchozí `dilna\`).

## v0.3.0 – 2026-09-30
**Přidáno**
- **Obrázky (🖼):** obrázek lze přiložit k zprávě z galerie/fotoaparátu nebo vložit ze schránky (Ctrl+V). Formáty PNG, JPEG, WebP, GIF. Velké obrázky se v prohlížeči zmenší na 1800 px (šetří tokeny i data). Obrázky se posílají modelu, který umí vidění (např. `openai/gpt-6-luna`, `anthropic/claude-sonnet-5.5`, `moonshotai/kimi-k3`); starší obrázky se vynechají, když by celý dotaz přesáhl 12 MB (`IMAGES_MAX_BYTES`).
- **Hledání v historii:** pole nad seznamem chatů hledá v názvech i ve zprávách, bez ohledu na diakritiku a velikost písmen („zlutoucky“ najde „žluťoučký“), s úryvkem textu. Escape hledání zruší.
- **Další parametry:** Top P, maximální počet tokenů odpovědi a hloubka uvažování (nízká/střední/vysoká). Platí globálně (⚙) i pro jednotlivý chat (✎), prázdné pole = výchozí hodnota. K teplotě lze nově nastavit hodnotu i pro jeden chat.
- **Spotřeba tokenů:** pod odpovědí se zobrazí počet tokenů vstup → výstup a u OpenRouteru i cena v USD (pokud ji poskytovatel pošle).
- **Srozumitelná chyba, když server neodpovídá** (dříve jen „Failed to fetch“): hláška říká, že se má zkontrolovat okno `start.bat` a adresa stránky.
- Export do Markdownu uvádí, ke které zprávě byly přiloženy obrázky.
- Databáze se sama migruje z v0.2 (existující data zůstanou).

**Neřešeno / známé omezení**
- Obrázky nelze přidat jako soubor projektu, jen ke zprávě. PDF, starý .doc/.xls a PowerPoint stále ne.
- Model bez podpory obrázků na zprávu s obrázkem odpoví chybou API (zobrazí se nad polem zprávy).
- Parametry `top_p`, `max_tokens` a `reasoning_effort` se posílají jen když jsou vyplněné; některá API některé z nich nepodporují.

## v0.2.0 – 2026-09-30
**Přidáno**
- **Projekty:** skupiny chatů s vlastními instrukcemi (platí pro všechny chaty v projektu). Výběr projektu v levém panelu, chat lze do projektu přesunout v nastavení chatu.
- **Soubory jako kontext:** tlačítko 📎 u zprávy (soubor patří chatu) a soubory projektu (platí pro všechny jeho chaty). Podporované formáty: text, kód, CSV, JSON, XML, HTML, Markdown, **DOCX**, **XLSX** (české kódování cp1250 se pozná samo). Limit 14 MB na soubor, 300 000 znaků textu na soubor a celkem 300 000 znaků příloh v jednom chatu (`FILES_MAX_CHARS`).
- **Uložené prompty (★):** knihovna vlastních promptů s hledáním, vložení do zprávy jedním klepnutím, úprava a mazání. Rozepsaný text lze rovnou uložit jako prompt.
- Databáze se sama migruje z v0.1 (existující data zůstanou).

**Neřešeno / známé omezení**
- PDF, starý .doc/.xls, PowerPoint a obrázky zatím nelze nahrát (obrázky ve v0.3, PDF později).
- Soubory se posílají modelu celé v každém dotazu, u dlouhých dokumentů se tedy spotřebuje víc tokenů.

## v0.1.0 – 2026-09-30
První funkční verze.

**Přidáno**
- Přihlášení (uživatel + heslo, hash scrypt, session v cookie), registrace vypnutá; účty se zakládají příkazem `npm run user`.
- Každý uživatel má oddělené konverzace i nastavení.
- Chat se streamováním odpovědí (SSE), tlačítko Stop (částečná odpověď se uloží), „↻ Znovu“ pro novou odpověď.
- Univerzální připojení: libovolné OpenAI-compatible API (Kimi mezinárodní/Čína, OpenAI, OpenRouter, DeepSeek, Groq, Ollama). Předvolby + vlastní Base URL, API klíč se ukládá jen na serveru.
- Výběr modelu (načtení seznamu z `/models` nebo ruční zadání), nastavení modelu a systémového promptu pro každý chat, volitelná teplota.
- Zobrazení „uvažování“ u modelů, které ho posílají (`reasoning_content`).
- Historie konverzací (SQLite), export chatu do Markdownu.
- Vykreslení Markdownu (nadpisy, seznamy, tabulky, citace, bloky kódu s tlačítkem Kopírovat).
- Mobilní i desktopové rozhraní, světlý/tmavý režim, PWA manifest a ikony.
- Bez externích závislostí (jen vestavěné moduly Node.js, žádné `npm install`).

**Známá omezení (plán na další verze)**
- Bez projektů, uložených promptů a nahrávání souborů (v0.2).
- Bez obrázků, hledání v historii a dalších parametrů (přidáno ve v0.3).
- Bez webového vyhledávání a nástrojů (v0.4).
- Žádné sdílení mezi uživateli.
