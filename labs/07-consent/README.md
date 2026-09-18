# Labb 7: Ett val som faktiskt styr insamlingen

## Syfte

Förstå samtycke som ett tekniskt tillstånd: ett val måste läsas och styra beteendet **innan** ett valfritt tracking-anrop skickas. Detta är ett förenklat undervisningsexempel med syntetiska data, inte en färdig lösning för verkliga samtyckeskrav.

## Mål

Du kan visa att ett event skickas när testtillståndet tillåter det, uteblir när tillståndet nekar det och börjar skickas igen om valet ändras. Du kan skilja ett gränssnitt som säger "nej" från kod som faktiskt stoppar requesten.

## Steg för steg

1. Starta grundappen. Öppna DevTools på **Console**, **Application → Local Storage** och **Network**. Rensa Network-listan. Utgå här från att knappens `POST /api/events` är *valfri analysinsamling*. Det finns ingen riktig beställning bakom knappen.
2. Lägg **först** i funktionen `sendBookingEvent()` i `public/app.js`, före `const event = createBookingEvent();`, följande kod:

   ```js
   if (localStorage.getItem("analyticsConsent") !== "yes") {
     statusMessage.textContent = "Analytics är avstängt. Inget event skickades.";
     return;
   }
   ```

3. Spara och ladda om. Klicka. Förutsäg och kontrollera: ser du `POST /api/events` i Network? Kontrollera att du inte förväxlar sidans vanliga filhämtningar med tracking-anropet.
4. Skriv `localStorage.setItem("analyticsConsent", "yes")` i Console. Klicka igen. Hitta `POST /api/events` och dess statuskod. Titta på det sparade värdet i Application.
5. Skriv `localStorage.setItem("analyticsConsent", "no")`. Klicka igen efter att du rensat Network-listan. Hitta det synliga meddelandet och kontrollera att ingen ny `POST /api/events` skickades.
6. Byt till `yes` igen, rensa Network-listan och klicka. Förklara varför kontrollen behöver ske *före* skapande och skickning av ett event. Om du gjorde labb 6, kontrollera också att datalagerposten inte skapas när funktionen returnerar tidigt.
7. Ta bort testnyckeln med `localStorage.removeItem("analyticsConsent")` och kontrollera att det nekade läget återkommer. Återställ kodändringen om du vill ha grundappen inför en annan labb.

## Facit: det du ser och varför

- Med värdet `yes` fortsätter funktionen och skickar eventet. Med `no` eller saknat värde avslutas den innan `fetch()` körs. Ett meddelande på sidan räcker inte; **avsaknaden av en ny POST i Network** är beviset för att klienten stoppade anropet.
- Själva testvalet ligger i webbläsarens Local Storage. Det är ett annat slags lagring än det event som försöker skickas till servern.
- Här finns bara en klientkontroll. Servern har ingen samtyckespolicy och kan inte veta om värdet i webbläsarens Local Storage är sant. Beskriv inte övningen som en fullständig lösning för juridiskt samtycke.

## Genomförd när

Du kan visa tre fall i Network: inget anrop utan tillstånd, ett accepterat anrop med `yes` och inget nytt anrop efter ändring till `no`. Förklara vilken kodrad som fattar beslutet och vilket led som skulle behöva mer arbete i ett verkligt system.
