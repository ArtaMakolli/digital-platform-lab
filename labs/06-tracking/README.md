# Labb 6: Från handling till tracking-event

## Syfte

Se hur en handling blir ett strukturerat event i klienten, hur en enkel `dataLayer` kan hålla en representation av eventet och hur ett separat HTTP-anrop skickar data till servern. Du ska kunna hitta första ledet som avviker när något går fel.

## Mål

Du kan visa eventnamn, parametrar och tidpunkt, jämföra klientens objekt med requestens payload, och se skillnaden mellan att lägga något i ett datalager och att faktiskt skicka det.

## Steg för steg

1. Starta appen och öppna `public/app.js`. Hitta `createBookingEvent()` och `sendBookingEvent()`. Förutsäg vilka fält ett klick skapar.
2. Lägg **överst** i `app.js` till `window.dataLayer = window.dataLayer || [];`. Lägg sedan direkt efter `const event = createBookingEvent();` i `sendBookingEvent()` till:

   ```js
   window.dataLayer.push({
     event: event.event,
     eventId: event.eventId,
     occurredAt: event.occurredAt,
     offeringId: event.context.offeringId,
   });
   console.log("Senaste tracking-event", window.dataLayer.at(-1));
   ```

3. Spara och ladda om. Öppna **Console** och **Network**. Klicka en gång. Läs `window.dataLayer` i Console och jämför senaste posten med **Payload** för `POST /api/events`. Notera att fältet `offeringId` ligger direkt på datalagerposten men under `context` i requestens objekt.
4. Gör ett andra klick. Jämför `eventId` och `occurredAt` i de två posterna. Ladda om sidan och skriv `window.dataLayer` igen: denna array skapades i minnet och byggs upp på nytt efter omladdningen.
5. Gör ett kontrollerat fel: kommentera tillfälligt bort raden `eventId: crypto.randomUUID(),` i `createBookingEvent()`. Spara, ladda om och klicka. Läs först Console/datalagret, sedan requesten och serverns **Response** i Network. Återställ raden och visa att servern åter svarar `202`.

## Facit: det du ser och varför

- `window.dataLayer.push(...)` lägger ett objekt i webbläsarens minne. **Det anropet skickar ingenting till servern.** Den befintliga `fetch()`-koden gör HTTP-anropet. Det finns ingen installerad tagghanterare i repot.
- När `eventId` saknas kan datalagerposten ändå skapas med `undefined` i det fältet, medan servern avvisar payloaden med `422` och beskriver vilket obligatoriskt fält som saknas. Det är ett exempel på att ett event kan finnas i klienten utan att bli accepterat av mottagaren.
- Koden visar serverns svar på sidan även när status är `422`. Läs därför både statuskod och response, inte bara att en response finns.
- Du kan lokalisera felet genom att jämföra **skapat objekt → datalager → request → serverns kvitto**. Ett accepterat syntetiskt event är fortfarande inte en riktig bokning eller en analysrapport.

## Genomförd när

Du kan visa två datalagerposter, en `POST`-request, ett avvisat anrop när `eventId` saknas och ett nytt accepterat anrop efter återställning. Förklara med ett konkret exempel skillnaden mellan *event skapat*, *request skickad* och *event accepterat*.
