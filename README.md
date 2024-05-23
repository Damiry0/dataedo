


Cześć,
poniżej znajduję parę szybkich uwag odnośnie przedstawionego rozwiązania. Część z nich jest również zależna od konwencji przyjętej w kodzie, a część jest dyskusyjna pod względem jej "krytyczności" o czym chętnie rozwinę temat w ramach dalszych etapów rekrutacji.


# Critical
* Brak authoryzacji tego kontrolera. Aktualnie dowolna osoba podająca id wskazanego użytkownika jest w stanie usunąć dany zasób. Można to rozwiąć na przykład przy użyciu modelu RBAC lub ABAC
* Brakuje również kontroli błędów. W przypadku nie znalezienia użytkownika operacja powinna zostać przerwana i zwrócony kod błędu (Http404) czy wyjątek ( z tym ostrożnie bo wyjątki są kosztowne)
* Dopisanie testów integracyjnych do kontrolera, przetestowanie E2E z poziomu aplikacji czy Postmanem

# Important
* Zamiana POST na DELETE gdyż zachowujemy wtedy idempotentność takiej operacji. Przy użyciu metody delete jesteśmy również w stanie skrócić ścieżkę naszego zapytania z „delete/{id}” na „{id:uint}” unikając redundancji.
* Zmiana metody na asynchroniczną wraz zamienieniem używanych metod na asynchroniczne odpowiedniki jak poniżej:
 ```
 public async Task Delete(...)
 {
    .FirstOrDefaultAsync(...)
    ...
    .SaveChangesAsync();  
 }
 ```
 * Idealnie w kontrolerze nie powinna znajdować się logika biznesowa. Można tutaj pomyśleć o przeniesieniu logiki do osobnego serwisu czy zastosowania wzorca mediatora
 * W ramach logowania informacji Id usuniętego użytkownika jest bardziej przydatne od jego użytkownika. Brak w zadaniu również wskazania czy ten log jest prostym debug info pozostawionym przez programiste w ramach testowania czy istotny aspekt biznesowy (np. konieczność przechowywania wszystkich logów w celach audytowych), możliwe że konieczne byłoby nawet dopisanie transakcji na operacje usunięcia rekordu jak i logowania
 * Ostatecznie metoda powinna zwrócić zamiast Http200 -> Http204

# Nice to have
* User user -> var user, kwestia preferencji 
* Fajnym dodatkiem było również dodanie CancelationTokena
* W ramach dobrej praktyki warto dodać również response code'y, ułatwia to szybkie zapoznawanie się z działaniem metody bez wnikania do kodu
```
  [ProducesResponseType(StatusCodes.Status204NoContent)]
```
*	Ze względów bezpieczeństwa można również pokusić się o zastanowienie nad użyciem GUID zamiast Uint. Zależy to oczywiście od tego większej ilości czynników: czy aplikacja została już wdrożona produkcyjnie, zastosowania aplikacji itd
