# Labb 9: Hitta första felet i kedjan

## Syfte

Felsöka med en prövbar hypotes och en ändring i taget. Du använder HTML, Console, Network och serverns svar för att hitta *var* ett dataflöde bryts innan du försöker laga det.

## Mål

Du kan beskriva förväntat och observerat beteende, visa ett bevis på den första avvikelsen, rätta ett litet fel och visa samma flöde fungerande igen.

## Steg för steg

1. Starta grundappen, klicka en gång och kontrollera att du ser `POST /api/events` med status `202`. Det är din fungerande jämförelse. Spara gärna en anteckning om förväntad kedja: **knapp → lyssnare → eventobjekt → request → serverns svar**.
2. Plantera **ett** fel: i `public/index.html`, byt knappens `id="booking-button"` till `id="booking-buton"`. Ändra inget i `app.js`. Spara och ladda om sidan.
3. Skriv din hypotes *före* nästa klick: *JavaScript hittar inte knappen, därför skickas inget POST-anrop.* Klicka och samla bevis i **Console** och **Network**. Läs felmeddelandet, hitta den aktuella väljaren i `public/app.js` och jämför den med HTML.
4. Återställ `id="booking-button"`. Ladda om, klicka och visa att samma test åter ger `202`. Förklara varför ändringen i HTML påverkade JavaScript trots att servern var opåverkad.
5. Fördjupning: plantera ett **nytt, separat** fel i `public/app.js` genom att tillfälligt ta bort `eventId` ur objektet i `createBookingEvent()`. Klicka igen. Nu finns ett `POST` i Network, men servern svarar `422`. Var sitter den första avvikelsen i den här versionen? Återställ raden och testa på nytt.
6. Om du redan har gjort labb 7 kan du även jämföra med `analyticsConsent = "no"`: då uteblir requesten utan att Console nödvändigtvis visar ett fel. Dokumentera det som ett *avsiktligt stopp*, inte som en trasig server.

## Facit: de tre olika observationerna

| Fall | Console | Network | Första relevanta led |
| --- | --- | --- | --- |
| Fel `id` i HTML | Fel när lyssnaren ska kopplas till saknat element | Ingen POST vid klick | Kopplingen mellan DOM och JavaScript |
| Saknat `eventId` | JavaScript kan köra vidare | POST finns, svar `422` | Eventets payload och serverns validering |
| Analys nekad i labb 7 | Kan vara utan fel | Ingen POST | Beslutet före insamling |

En utebliven request kan alltså ha flera orsaker. Skillnaden syns när du undersöker ett led i taget. Svar `202` efter återställning visar att just ditt testflöde fungerar igen; det bevisar inte att alla tänkbara fel i en större plattform är lösta.

## Genomförd när

Du kan visa ett fungerande utgångsläge, ett planterat fel, din hypotes och beviset som lokaliserar det, samt ett nytt fungerande test efter återställning. Skriv en kort rapport: **Förväntat → observerat → hypotes → minsta test → slutsats och osäkerhet.**
