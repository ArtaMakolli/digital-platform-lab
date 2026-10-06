# Labb 6: Från handling till tracking-event

## Syfte

Se hur en handling blir ett strukturerat event i klienten, hur en enkel `dataLayer` kan hålla en representation av eventet och hur ett separat HTTP-anrop skickar data till servern. Du ska kunna hitta första ledet som avviker när något går fel.

## Mål

Du kan visa eventnamn, parametrar och tidpunkt, jämföra klientens objekt med requestens payload, och se skillnaden mellan att lägga något i ett datalager och att faktiskt skicka det.

## Steg för steg

1. Starta appen och öppna `public/app.js`. Hitta `createBookingEvent()` och `sendBookingEvent()`. Förutsäg vilka fält ett klick skapar.
2. Nu ska du ändra **`public/app.js` på två olika ställen**. Gör del A först och därefter del B.

   ### A. Lägg till `dataLayer` högst upp i filen

   Kontrollera först att du har öppnat filen **`public/app.js`** – inte någon annan JavaScript-fil.

   Gå allra högst upp i filen och lägg till den här raden:

   ```js
   window.dataLayer = window.dataLayer || [];
   ```

   Raden ska ligga **utanför alla funktioner**, högst upp i filen. Den skapar den array som vi använder som ett enkelt datalager i webbläsaren.

   ### B. Lägg tracking-koden inne i `sendBookingEvent()`

   Stanna kvar i **`public/app.js`**.

   1. Leta upp funktionen som heter **`sendBookingEvent()`**.
   2. Inne i den funktionen letar du upp exakt den här raden:

      ```js
      const event = createBookingEvent();
      ```

   3. Placera markören **direkt på raden UNDER** `const event = createBookingEvent();`.
   4. Klistra in följande kod där:

      ```js
      window.dataLayer.push({
        event: event.event,
        eventId: event.eventId,
        occurredAt: event.occurredAt,
        offeringId: event.context.offeringId,
      });
      console.log("Senaste tracking-event", window.dataLayer.at(-1));
      ```

   **Viktigt:** Lägg inte kodblocket högst upp i filen och skapa inte en ny funktion. Koden ska ligga **inne i den befintliga `sendBookingEvent()`**, direkt efter att eventet har skapats.

   När du är klar ska ordningen inne i funktionen alltså vara:

   ```js
   const event = createBookingEvent();

   window.dataLayer.push({
     event: event.event,
     eventId: event.eventId,
     occurredAt: event.occurredAt,
     offeringId: event.context.offeringId,
   });
   console.log("Senaste tracking-event", window.dataLayer.at(-1));

   // Här fortsätter den kod som redan fanns i funktionen.
   ```

   Spara filen. Du ska **inte ta bort eller ersätta den befintliga koden** i `sendBookingEvent()`; du lägger bara till tracking-koden på rätt plats.

3. Nu ska du **se vad som faktiskt händer i webbläsaren** när du klickar.

   ### A. Öppna DevTools och Console

   1. Spara `public/app.js`.
   2. Ladda om sidan i webbläsaren.
   3. Öppna **DevTools**. Du kan till exempel högerklicka på sidan och välja **Inspect/Inspektera**.
   4. Klicka på fliken **Console**.
   5. Gör **ett klick i appen som skapar/skickar bokningseventet**.
   6. Gå tillbaka till **Console**.
   7. Skriv exakt:

      ```js
      window.dataLayer
      ```

      och tryck **Enter**.

   Du bör nu se något som börjar ungefär med **`Array(1)`**. Det betyder att `dataLayer` är en array och att den just nu innehåller **ett objekt**.

   Klicka på den lilla **pilen/triangeln** bredvid `Array(1)` för att expandera arrayen. Du ser då posten med index **`0`**. Klicka på pilen bredvid **`0`** för att expandera även objektet.

   Leta efter:

   - `event` – namnet på det som hände.
   - `eventId` – ett unikt id för just detta event.
   - `occurredAt` – tidpunkten då eventet skapades.
   - `offeringId` – vilken offering/händelse eventet gäller.

   **Det du tittar på nu är data som finns i webbläsaren.** Att objektet finns i `window.dataLayer` betyder inte i sig att något har skickats till servern.

   ### B. Se vad som skickades till servern i Network

   1. Klicka på fliken **Network** i DevTools.
   2. Om listan är tom: gör **ett nytt klick i appen** medan Network är öppet.
   3. Leta efter requesten som heter **`events`** eller **`/api/events`**. Du kan använda Network-flikens filterfält och skriva `events` om det finns många requests.
   4. Klicka på requesten.
   5. Öppna delen/fliken **Payload**.
   6. Expandera objekten med de små pilarna om de är ihopfällda.

   Leta efter samma information som du nyss såg i `window.dataLayer`: `event`, `eventId`, `occurredAt` och `offeringId`.

   Här finns en viktig skillnad att upptäcka: i `dataLayer` ligger **`offeringId` direkt på objektet**, medan det i requestens payload ligger **under `context`**. Expandera därför `context` för att hitta det.

   **Vad har du just sett?**

   `window.dataLayer` visar den representation vi lagt i webbläsarens minne. **Network → Payload** visar den data som faktiskt skickades i HTTP-requesten till servern.

4. Nu ska du skapa **två event och jämföra dem**.

   1. Gå tillbaka till appen och gör ett nytt klick.
   2. Öppna **Console** igen.
   3. Skriv:

      ```js
      window.dataLayer
      ```

      och tryck **Enter**.

   Nu bör du se exempelvis **`Array(2)`**. Expandera arrayen med pilen. Där ska finnas två poster, normalt **`0`** och **`1`**.

   Expandera båda objekten och jämför särskilt:

   - `eventId` – de två eventen ska ha olika id.
   - `occurredAt` – de skapades vid olika tidpunkter.

   Det visar att varje klick skapar **ett nytt event**, även om du utför samma handling.

   ### Vad händer om sidan laddas om?

   1. Ladda om webbsidan.
   2. Öppna **Console**.
   3. Skriv återigen:

      ```js
      window.dataLayer
      ```

      och tryck **Enter**.
   4. Expandera resultatet.

   Nu bör arrayen vara tom, exempelvis **`Array(0)`**, tills du klickar igen.

   **Varför?** Vår `dataLayer` finns bara i webbläsarens minne. När sidan laddas om körs JavaScript-filen från början och arrayen skapas på nytt. Vi har inte sparat den i exempelvis `localStorage` eller en databas.

5. Nu ska du **medvetet skapa ett fel och följa det genom hela flödet**. Det här är felsökning: vi vet vilket fel vi skapar och undersöker hur det syns på olika ställen.

   ### A. Skapa felet i koden

   1. Gå tillbaka till **`public/app.js`** i VS Code.
   2. Leta upp funktionen **`createBookingEvent()`**.
   3. Leta inne i funktionen efter raden:

      ```js
      eventId: crypto.randomUUID(),
      ```

   4. Kommentera tillfälligt bort den genom att sätta `//` framför raden:

      ```js
      // eventId: crypto.randomUUID(),
      ```

   5. Spara filen.
   6. Ladda om webbsidan.

   Du har nu medvetet tagit bort ett obligatoriskt fält från eventet.

   ### B. Se felet först i Console/dataLayer

   1. Gör **ett klick i appen**.
   2. Öppna **Console**.
   3. Skriv:

      ```js
      window.dataLayer
      ```

      och tryck **Enter**.
   4. Expandera arrayen och sedan objektet med pilarna.
   5. Titta på `eventId`.

   `eventId` kan fortfarande synas på datalagerposten, men värdet är **`undefined`**. Det betyder ungefär: *fältet finns i objektet vi byggde för dataLayer, men det har inget värde*.

   Det viktiga här är att **dataLayer fortfarande kan få en post trots att eventet är felaktigt**. dataLayer kontrollerar inte om servern kommer att godkänna eventet.

   ### C. Följ samma klick till Network

   1. Öppna **Network**.
   2. Om du behöver, gör ett nytt klick med Network öppet.
   3. Leta efter **`events` / `POST /api/events`** och klicka på requesten.
   4. Titta först under **Payload**. Kontrollera hur requesten ser ut när `eventId` saknas.
   5. Titta sedan på requestens **Status**. Den ska vara **`422`**.
   6. Öppna **Response** och läs serverns svar.

   **422 betyder här att servern tog emot requesten men inte accepterade innehållet eftersom ett obligatoriskt fält saknas.**

   Du har nu följt felet genom kedjan:

   **JavaScript skapar eventet → dataLayer får en post → HTTP-request skickas → servern granskar datan → servern svarar 422.**

   ### D. Rätta felet igen

   1. Gå tillbaka till **`public/app.js`**.
   2. Ta bort `//` så att raden återigen är:

      ```js
      eventId: crypto.randomUUID(),
      ```

   3. Spara filen och ladda om sidan.
   4. Öppna **Network** och gör ett nytt klick.
   5. Klicka på requesten **`events` / `POST /api/events`**.
   6. Kontrollera statuskoden.

   Nu ska servern åter svara **`202`**.

   **202 betyder här att servern har accepterat vårt syntetiska event.** Jämför detta med 422-felet du nyss skapade. Du har alltså inte bara sett ett felmeddelande – du har följt var felet uppstod och hur det påverkade nästa steg i flödet.

## Facit: det du ser och varför

- `window.dataLayer.push(...)` lägger ett objekt i webbläsarens minne. **Det anropet skickar ingenting till servern.** Den befintliga `fetch()`-koden gör HTTP-anropet. Det finns ingen installerad tagghanterare i repot.
- När `eventId` saknas kan datalagerposten ändå skapas med `undefined` i det fältet, medan servern avvisar payloaden med `422` och beskriver vilket obligatoriskt fält som saknas. Det är ett exempel på att ett event kan finnas i klienten utan att bli accepterat av mottagaren.
- Koden visar serverns svar på sidan även när status är `422`. Läs därför både statuskod och response, inte bara att en response finns.
- Du kan lokalisera felet genom att jämföra **skapat objekt → datalager → request → serverns kvitto**. Ett accepterat syntetiskt event är fortfarande inte en riktig bokning eller en analysrapport.

## Genomförd när

Du kan visa två datalagerposter, en `POST`-request, ett avvisat anrop när `eventId` saknas och ett nytt accepterat anrop efter återställning. Förklara med ett konkret exempel skillnaden mellan *event skapat*, *request skickad* och *event accepterat*.
