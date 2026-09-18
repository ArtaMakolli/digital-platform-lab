# Labb 4: Vad minns webbläsaren?

## Syfte

Undersöka skillnaden mellan ett värde som bara finns medan JavaScript körs, `sessionStorage`, `localStorage` och en enkel cookie. Det förklarar hur en tjänst kan minnas något mellan sidvisningar och varför ett värde ibland försvinner.

## Mål

Du kan själv skapa och hitta ett värde, ladda om sidan och säga vad som överlevde. Du kan visa var värdet finns i DevTools och skilja lokal lagring från ett anrop till servern.

## Steg för steg

1. Starta appen och öppna `http://localhost:3000`. Öppna DevTools. Välj **Console** för att köra de små experimenten nedan. Välj sedan **Application** (i vissa webbläsare under `>>`) för att hitta **Local Storage**, **Session Storage** och **Cookies** för `http://localhost:3000`.
2. Skriv `let testValue = "första besöket"` i Console och sedan `testValue`. Förutsäg vad som händer när du laddar om sidan. Ladda om och försök läsa `testValue` igen. En variabel skapad i sidans Console finns normalt inte kvar efter en omladdning.
3. Skriv `sessionStorage.setItem("lab4Session", "hej")` och `localStorage.setItem("lab4Local", "hej")`. Leta upp båda i Application. Ladda om och läs med `sessionStorage.getItem("lab4Session")` respektive `localStorage.getItem("lab4Local")`.
4. Stäng fliken och öppna sidan igen. Jämför värdena. Prova sedan att stänga webbläsaren och öppna den igen. Anteckna vad *just din webbläsare* gjorde. Återställning av tidigare flikar kan göra sessionstestet mindre entydigt, så skriv inte en generell regel utifrån ett enda försök.
5. Skriv `document.cookie = "lab4Cookie=hej; path=/; SameSite=Lax"`. Hitta cookien i Application. Ladda om och se om den finns kvar. En cookie utan `Max-Age`/`Expires` är avsedd för sessionen, men webbläsarens återställning kan påverka vad du observerar.
6. Radera `lab4Local` i Application och ladda om. Läs `localStorage.getItem("lab4Local")`. Rensa sedan dina testvärden med `sessionStorage.removeItem("lab4Session")` och `document.cookie = "lab4Cookie=; path=/; Max-Age=0"`.
7. Öppna Network och klicka på **Send booking event**. Kontrollera requestens payload. Att du sparade `lab4Local` betyder **inte** att värdet automatiskt skickades med i `fetch()`-anropet.

## Facit: det du ser och varför

- En vanlig variabel hör till sidans aktuella körning. `sessionStorage` hör till webbläsarens session för en viss origin och flik. `localStorage` hör till en origin och finns normalt kvar tills den tas bort. Detta är mönster att jämföra med dina egna observationer, inte ett löfte om exakt beteende efter återställning av webbläsaren.
- `localStorage` och `sessionStorage` skickas inte automatiskt som HTTP-header. JavaScript måste läsa ett värde och lägga till det i ett anrop om servern ska få det.
- Cookies har egna regler för domän, sökväg, livslängd och när de skickas. Kontrollera **Request Headers** i Network om du vill se en cookie skickas med. Detta skiljer sig från `localStorage`.
- Application visar webbläsarens lagring. Den säger i sig inte att servern har sparat samma värde.

## Genomförd när

Du kan visa minst ett värde i vardera Session Storage, Local Storage och Cookies, jämföra dem före och efter omladdning, ta bort ett värde och förklara varför `lab4Local` inte automatiskt finns i eventets JSON-payload. Anteckna **förväntat, observerat och förklaring** för varje steg.
