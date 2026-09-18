# Labb 8: Vad bekräftar servern?

## Syfte

Jämföra det klienten skickar med det servern faktiskt bekräftar. Se skillnaden mellan ett mottaget event, en affärshändelse och en rapport. Undersök också om samma event-id räknas som en dubblett av den nuvarande servern.

## Mål

Du kan läsa metod, payload, status och svar i Network och säga exakt vad ett `202`-svar visar. Du kan testa två likadana event och dra en slutsats om den nuvarande serverns beteende.

## Steg för steg

1. Starta grundappen och öppna **Network**. Rensa listan, klicka på **Send booking event** och öppna `POST /api/events`.
2. Anteckna tre saker från **Payload**: `event`, `eventId` och `occurredAt`. Anteckna tre saker från **Response**: `accepted`, `receiptId` och `receivedAt`. Jämför det skickade `eventId` med det i svaret.
3. Titta i terminalen där `npm start` körs. Hitta raden `Event accepted`. Vilka fält skriver servern där? Vilken information finns i requesten men inte i serverloggen eller kvittot?
4. Prova en dubblett utan att ändra projektfilen. Kör detta i DevTools **Console** på sidan:

   ```js
   const duplicate = {
     event: "booking_submitted",
     eventId: "same-id-twice",
     occurredAt: new Date().toISOString()
   };
   const sendDuplicate = () => fetch("/api/events", {
     method: "POST",
     headers: { "Content-Type": "application/json" },
     body: JSON.stringify(duplicate)
   });
   console.log(await (await sendDuplicate()).json());
   console.log(await (await sendDuplicate()).json());
   ```

5. Se de två `POST`-raderna i Network. Jämför `eventId`, statuskoder och `receiptId`. Skriv vad servern gjorde, inte vad en framtida analysplattform *borde* göra.
6. Valfritt kontrollerat fel: skicka samma objekt utan `eventId` och läs status `422` och feltexten. Återställ inte något i projektkoden eftersom du bara testade via Console.

## Facit: det du ser och varför

- Servern returnerar `202` och ett kvitto när den har validerat ett event. Den här appen lagrar ingen bokning och bygger ingen rapport. Ett kvitto visar inte att en betalning skett eller att ett analysverktyg registrerat en konvertering.
- De två likadana `eventId` accepteras var för sig och får olika `receiptId`. **Det finns ingen deduplicering i denna server.** Samma affärshändelse skulle alltså kunna rapporteras två gånger om ett senare system behandlade båda som unika.
- Ett event utan obligatoriskt `eventId` avvisas med `422`. En response i Network kan alltså betyda antingen accepterat eller avvisat; läs status och innehåll.

## Genomförd när

Du kan visa request, `202`-kvitto, serverlogg och två anrop med samma `eventId`. Avsluta med två meningar: *Det här bekräftar servern. Det här kan jag ännu inte påstå.*
