# Labb 1: Från utvecklingsmiljö till ett klick som når servern

## Syfte

Sätta upp och undersöka en utvecklingsmiljö. GitHub, Git, GitHub Desktop, VS Code, terminalen, Node.js, den lokala servern och webbläsaren är delar av ett tekniskt ekosystem. Du behöver kunna hitta var i kedjan något händer, även när du får ett nytt projekt på en arbetsplats.

## Mål

Du kan hämta repot, öppna rätt projektmapp, starta servern, öppna sidan på localhost, klicka på **Send booking event** och visa vad webbläsaren skickar och vad servern svarar. Du kan säga vad du *observerar*, vad du *sluter dig till* och vad du ännu *inte vet*.

## Förbered din miljö

Du behöver Git, GitHub Desktop (Windows eller macOS), Visual Studio Code och Node.js 20 eller senare. Installera dem om de saknas. Starta om terminalen efter en Node-installation. På Windows använder vi Windows-miljön först; Ubuntu/WSL är en separat fördjupning och behövs inte för den här labben.

| Miljö | Hämta och öppna projektet | Starta servern | Öppna DevTools |
| --- | --- | --- | --- |
| Windows | Fork på GitHub, sedan **Code → Open with GitHub Desktop → Clone → Open in Visual Studio Code** | I VS Codes PowerShell: `npm.cmd start` | `F12` eller `Ctrl+Shift+I`; på vissa tangentbord `Fn+F12` |
| macOS | Fork, klona med GitHub Desktop, öppna i VS Code | `npm start` | `⌥+⌘+I` (Option+Command+I) |
| Linux | Klona din fork med Git eller använd Codespaces, öppna projektmappen i VS Code | `npm start` | `Ctrl+Shift+I` |
| Chromebook | Använd Codespaces för editor och terminal | `npm start` i Codespaces | `Ctrl+Shift+I` i webbläsaren |

GitHub Desktop finns normalt inte på Linux eller Chromebook. `github.dev` är en webbeditor utan den körande Node-server som just denna labb behöver; använd Codespaces om du behöver en webbaserad utvecklingsmiljö.

1. Öppna [kursrepot](https://github.com/MatteoDiAmare/digital-platform-lab) på GitHub och välj **Fork**. Kontrollera att din egen GitHub-profil nu har ett repo som heter `digital-platform-lab`.
2. Klona **din fork** med GitHub Desktop, eller använd Git/Codespaces enligt tabellen. En fork är ditt repo på GitHub. En clone är projektmappen på din dator. Git håller reda på ändringarna mellan dem.
3. Öppna hela mappen `digital-platform-lab` i VS Code. I filpanelen ska du se `package.json`, `server.js`, `public/` och `labs/`. Stäng gärna VS Codes välkomstflik om den döljer projektet.
4. Öppna **Terminal → New Terminal**. Terminalen ska stå i projektmappen. Kör `git --version`, `node --version` och `npm --version` (på Windows kan du använda `npm.cmd --version` om PowerShell blockerar `npm`). Om ett kommando inte känns igen: kontrollera installationen och starta om terminalen.
5. Starta med kommandot i tabellen. Terminalen ska visa `Digital Platform Lab is running at http://localhost:3000`. Låt terminalen vara öppen.
6. Öppna `http://localhost:3000` i webbläsaren **på samma dator**. `localhost` betyder din egen dator; i Codespaces öppnar du port **3000** i fliken **Ports** och använder den vidarebefordrade webbadressen.

## Undersök flödet

1. Skriv före klicket: *Jag förväntar mig att webbläsaren skickar ett event och får ett svar från servern.*
2. Öppna DevTools och välj **Network**. Rensa listan med knappen för att rensa nätverksloggen. Om listan är tom, ladda om sidan en gång och se vilka filer som hämtas.
3. Klicka på **Send booking event**. Hitta raden för `events` eller `/api/events` i Network. Klicka på den.
4. Läs **Headers**: adressen `/api/events`, metoden `POST` och status `202`. Läs **Payload** eller **Request**: där finns bland annat `event`, `eventId` och `occurredAt`. Läs **Response**: där finns `accepted`, `eventId`, `receiptId` och `receivedAt`.
5. Titta på sidan: statusen och eventloggen ändras. Titta också i VS Codes terminal: servern skriver `Event accepted`. Förklara vilken del du ser i webbläsaren och vilken del som körs i Node.
6. Prova `http://localhost:3000/api/health`. Förklara varför det är ett annat anrop än knappens `POST /api/events`.

**DevTools-karta:** Elements visar sidans DOM; Console visar meddelanden och JavaScript-fel; Network visar HTTP-anrop och svar; Application visar bland annat lokal lagring. I den här labben använder du framför allt Network. Ändringar som du gör direkt i Elements är tillfälliga och återställs normalt när sidan laddas om.

## Facit: vad säger observationen?

- Ett klick kör JavaScript i `public/app.js`, som skapar ett objekt och använder `fetch()` för att skicka JSON till servern i `server.js`.
- `202` och `accepted: true` visar att servern accepterade **detta event** och gav ett kvitto. Det bevisar inte att en riktig bokning skapades eller sparades permanent. Exemplet använder syntetiska data.
- `eventId` skapades i webbläsaren; `receiptId` skapades av servern. Du kan se skillnaden i request och response.
- Ett tomt eller felaktigt localhost-fönster betyder först att du kontrollerar terminal, rätt mapp, rätt port och om servern fortfarande kör. Stoppa servern med `Ctrl+C` när du är färdig.

## Genomförd när

Du kan visa projektmappen och den körande servern, ett knappklick, dess `POST`-rad i Network samt skillnaden mellan request och response. Anteckna **en observation, en rimlig slutsats och en sak som svaret inte bevisar**. Gör först labben tillsammans, upprepa sedan själv så att du hittar stegen igen.
