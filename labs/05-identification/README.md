# Labb 5: Ett id är inte en person

## Syfte

Undersöka hur ett tekniskt `visitorId` kan binda ihop flera handlingar i samma webbläsare och vad identifieraren inte bevisar. Du bygger en enkel modell i klienten; kursappens server skapar inga användarkonton och sammanfogar inga personer.

## Mål

Du kan skapa ett id en gång, spara det i webbläsaren, skicka det i ett event och förklara vad som händer efter omladdning, raderad lagring eller en annan webbläsare.

## Steg för steg

1. Starta appen. Öppna `public/app.js`. Skriv först din förväntan: *Kommer samma webbläsare att använda samma id för två klick och efter omladdning?*
2. Lägg följande hjälpfunktion **före** `createBookingEvent()`:

   ```js
   function getVisitorId() {
     let id = localStorage.getItem("visitorId");
     if (!id) {
       id = crypto.randomUUID();
       localStorage.setItem("visitorId", id);
     }
     return id;
   }
   ```

3. Lägg till `visitorId: getVisitorId(),` i objektet `context` i `createBookingEvent()`. Spara och ladda om.
4. Öppna DevTools **Application → Local Storage** och **Network**. Klicka två gånger på knappen. Jämför `context.visitorId` och `eventId` i de två requesternas payload. `visitorId` bör vara samma medan `eventId` ändras vid varje klick.
5. Ladda om sidan och klicka igen. Läs id:t i både Local Storage och Network. Ta sedan bort nyckeln `visitorId` i Application, ladda om och klicka på nytt. Vilket id fick du nu?
6. Öppna samma localhost-adress i en annan webbläsare eller en privat session. Om du använder Codespaces ska du öppna **samma vidarebefordrade adress** i båda testerna; `localStorage` är knutet till en origin. Jämför id:n utan att anta att servern vet vem som sitter vid datorn.

## Facit: det du ser och varför

- Funktionen återanvänder ett sparat id eller skapar ett nytt. Två event kan därför ha samma `visitorId` men olika `eventId`.
- En raderad nyckel gör att nästa besök skapar ett nytt id. En annan webbläsare har normalt egen lokal lagring, även om samma människa använder den.
- Requestens payload visar att klienten **skickade** id:t. Servern i denna kursapp kontrollerar eventets toppfält och svarar med ett kvitto, men sparar inte besökarprofiler eller sammanfogar historik. Svarskod `202` bevisar inte sådan koppling.
- Två personer som delar en webbläsare kan dela samma sparade id. Ett `visitorId` visar därför inte säkert en fysisk person.

## Genomförd när

Du kan visa två request med samma `visitorId` och olika `eventId`, skapa ett nytt `visitorId` genom att radera lagringen och förklara en situation där **en person får två id** och en där **två personer delar ett id**. Återställ gärna testlagringen efteråt.
