# Labb 3: Ett klick blir ett JavaScript-event

## Syfte

Följa kopplingen mellan HTML-knappen, en händelselyssnare, ett JavaScript-objekt och anropet till servern. Du ska se hur en liten kodändring påverkar det som skickas.

## Mål

Du kan hitta koden som körs när du klickar, ändra ett värde i eventet, visa det nya värdet i Network och skilja på JavaScript-eventet och serverns kvitto.

## Steg för steg

1. Starta appen och öppna `public/app.js`. Leta längst ner efter `bookingButton.addEventListener("click", sendBookingEvent)`. Hitta sedan funktionerna `sendBookingEvent()` och `createBookingEvent()`.
2. Skriv före klicket vilka fält du tror kommer att finnas i requesten. Öppna DevTools på **Network**, rensa listan och klicka på knappen. Läs **Payload** för `POST /api/events`.
3. I `createBookingEvent()`, ändra det syntetiska värdet `offeringId: "platform-intro"` till `offeringId: "my-test-offering"`. Spara, ladda om, klicka igen och hitta värdet i Network. Återställ värdet när du har sett resultatet.
4. Lägg till raden `console.log("Event före skickning", event);` direkt efter `const event = createBookingEvent();` i `sendBookingEvent()`. Spara, ladda om och klicka. Jämför objektet i **Console** med requesten i **Network**.
5. Klicka en gång till och jämför `eventId` i de två requesterna. Titta även på `receiptId` i de två svaren. Varför är de olika?
6. Gör ett litet avsiktligt fel: byt `document.querySelector("#booking-button")` till `document.querySelector("#saknas")`. Ladda om och läs felraden i Console. Återställ sedan väljaren och kontrollera att klicket fungerar igen.

## Facit: det du ser

- `addEventListener` knyter klicket till funktionen. `createBookingEvent()` bygger objektet i webbläsaren. `fetch()` skickar JSON över HTTP.
- Fältet `context.offeringId` följer med i requesten efter ändringen. Servern validerar bara vissa toppfält och svarar med ett kvitto; svaret innehåller inte automatiskt alla fält du skickade.
- `crypto.randomUUID()` ger ett nytt `eventId` per klick. Servern skapar ett eget `receiptId` per accepterat anrop.
- En trasig CSS-väljare ger ett fel i webbläsaren innan requesten skickas. Om du inte ser något `POST` i Network, börja i Console och följ kedjan framåt.
- Svarskod `202` innebär accepterat event, inte att en bokning lagrats i en databas.

## Genomförd när

Du kan visa ett ändrat värde i requestens payload, peka på kodraden som skapade det, visa skillnaden mellan `eventId` och `receiptId` och återställa den avsiktligt trasiga väljaren. Skriv en kort kedja från **knapp → funktion → objekt → request → svar**.
