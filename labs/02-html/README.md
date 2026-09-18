# Labb 2: HTML, DOM och den synliga sidan

## Syfte

Se hur HTML-filen blir en sida i webbläsaren och hur JavaScript hittar element i sidans DOM. Du ska våga ändra en liten sak, kontrollera resultatet och kunna återställa ändringen.

## Mål

Du kan hitta en synlig text och en knapp i `public/index.html`, ändra dem, se ändringen på localhost och förklara vad ett `id` används till i den här appen.

## Steg för steg

1. Starta servern enligt labb 1 och öppna `http://localhost:3000`. Ha VS Code och webbläsaren bredvid varandra. Öppna DevTools på fliken **Elements**.
2. Läs `public/index.html`. Hitta `<h1>`, knappen med `id="booking-button"` och området med `id="status"`. Förutsäg vilken text på sidan som motsvarar varje element.
3. Ändra rubrikens text i `<h1>`, spara filen och ladda om sidan. Jämför filen, **Elements** och det du ser på skärmen. Återställ gärna rubriken efteråt.
4. Ändra texten **Send booking event** mellan knappens taggar till en egen tydlig text. Spara och ladda om. Klicka. Knappen ska fortfarande fungera: dess synliga text har ändrats, men dess `id` är samma.
5. Gör nu ett kontrollerat fel: byt knappens `id` i HTML från `booking-button` till `booking-test`, men låt `public/app.js` vara oförändrad. Ladda om, klicka och titta i **Console**. Vad förväntade du dig? Vad hände?
6. Återställ `id="booking-button"`, spara och ladda om. Klicka igen och kontrollera i **Network** att `POST /api/events` syns.
7. Högerklicka på knappen och välj **Inspect/Inspektera**. Webbläsaren markerar motsvarande DOM-element i Elements. Gör en tillfällig textändring där och ladda om. Jämför med ändringen du gjorde i filen.

## Facit: det du ser

- HTML anger struktur och innehåll. Webbläsaren bygger en DOM som du kan inspektera i Elements. CSS styr utseendet; JavaScript kan reagera på knappen.
- När du ändrar texten i projektfilen och laddar om finns ändringen kvar. En ändring direkt i Elements ändrar bara den aktuella sidan och försvinner vid omladdning.
- JavaScript använder `document.querySelector("#booking-button")` för att hitta knappen. När du ändrar dess `id` enbart i HTML hittar koden den inte längre. Console visar ett fel när koden försöker lägga en lyssnare på ett saknat element. Det är en annan sorts fel än att servern avvisar ett event.
- Att knapptexten ändras påverkar inte kopplingen så länge `id` är oförändrat.

## Genomförd när

Du kan visa en textändring i HTML, peka ut samma element i Elements, förklara varför ett ändrat `id` bröt klicket och återställa det så att `POST /api/events` fungerar igen. Skriv kort: *Vad ändrade jag? Vad förväntade jag mig? Vad observerade jag? Varför?*
