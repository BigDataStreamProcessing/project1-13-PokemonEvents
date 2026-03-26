# Charakterystyka danych
Pokemony to fikcyjne stworzenia, które można złapać, by dołączyły do naszej drużyny. 
Każdy z pokemonów posiada: swoją nazwę, typ oraz liczbę cp (combat power). 
Dane opisują strumień danych reprezentujący uporządkowane w czasie zdarzenia 
łapania pokemonów z danymi parametrami.

W strumieniu pojawiają się zdarzenia zgodne ze schematem `PokemonEvent`.

```
create json schema PokemonEvent(type string, name string, cp int, ets string, its string);
```

Każde zdarzenie związane z jest z faktem złapania pokemona o
określonej sile (`cp`) i określonym typie.


Dane uzupełnione są o dwie etykiety czasowe. 
* Pierwsza (`ets`) związana jest z momentem złapania pokemona. 
  Etykieta ta może się losowo spóźniać w stosunku do czasu systemowego 
  maksymalnie do 30 sekund.
* Druga (`its`) związana jest z momentem rejestracji zdarzenia złapania 
  pokemona w systemie.

# Opis atrybutów

Atrybuty w każdym zdarzeniu zgodnym ze schematem `PokemonEvent` 
mają następujące znaczenie:

* `name` - nazwa złapanego pokemona
* `type` - typ złapanego pokemona
* `cp` - liczba oznaczająca siłę pokemona
* `ets` - czas złapania pokemona
* `its` - czas rejestracji faktu złapania pokemona w systemie

# Zadania
Opracuj rozwiązania poniższych zadań. 
* Opieraj się strumieniu zdarzeń zgodnych ze schematem `PokemonEvent`
* W każdym rozwiązaniu możesz skorzystać z jednego lub kilku poleceń EPL.
* Ostatnie polecenie będące ostatecznym rozwiązaniem zadania musi 
  * być poleceniem `select` 
  * posiadającym etykietę `answer`, przykładowo:
  ```aidl
    @name('answer') SELECT type, name, cp, ets, its 
    FROM PokemonEvent#ext_timed(java.sql.Timestamp.valueOf(its).getTime(), 3 sec);
  ```

## Zadanie 1
Dla każdego typu podawaj największą wartość siły (`cp`) wśród złapanych 
pokemonów danego typu. 
Analizowane dane ograniczaj tylko do tych, które zostały zarejestrowane 
w ciągu ostatnich 30 sekund.

Wyniki powinny zawierać, następujące kolumny:
- `type` - typ złapanego pokemona,
- `highest_cp` - wartość cp najsilniejszego pokemona danego typu.

## Zadanie 2
Wyświetlaj informacje o przypadkach złapania bardzo silnych pokemonów, 
czyli takich, których wartość siły jest większa niż 4800.

Wyniki powinny zawierać, następujące kolumny:
- `ets` - czas złapania pokemona,
- `cp` - wartość siły złapanego pokemona,
- `type` - typ złapanego pokemona,
- `name` - nazwę złapanego pokemona.

## Zadanie 3 
Wyświetlaj informacje o przypadkach złapania pokemona o największej 
sile spośród pokemonów tego samego typu, których złapanie zostało 
zarejestrowane w ciągu ostatnich 30 sekund.

Wyniki powinny zawierać, następujące kolumny:
- `its` - czas rejestracji złapania pokemona,
- `cp` - wartość siły złapanego pokemona,
- `type` - typ złapanego pokemona,
- `name` - nazwę złapanego pokemona.

## Zadanie 4
Załóż, że sesje łapania pokemonów odbywają się co 10 sekund — 
liczy się czas rejestracji przypadku złapania pokemona. 
Dla każdej sesji wyznaczane są dominujące typy pokemonów, które w 
jej trakcie łapane były największą liczbę razy. 
5 kolejnych sesji tworzy rozgrywkę. Dla każdej rozgrywki wyznaczane 
są `DType` (typy pokemonów), które były dominujące w co najmniej trzech 
sesjach zaliczanych do rozgrywki. 

Wyznaczaj typ `DType` dla rozgrywek. 

Wyniki powinny zawierać, następujące kolumny:
- `DType` - typ `Dtype` dla rozgrywki.
- `start_its` - czas rejestracji złapania pierwszego pokemona 
   w pierwszej sesji pierwszej rozgrywki
- `end_its` - czas rejestracji złapania ostatniego pokemona w 
  ostatniej sesji ostatniej rozgrywki

W przypadku gdy wiele typów pokemonów będzie wyznaczone dla jednej rozgrywki,
każdy z nich raportowany jest w osobnym wierszu wynikowym.

## Zadanie 5
Pokemony, których złapanie zostało zarejestrowane w ciągu 5 sekund, 
wchodzą w interakcję, o ile ich typy są rywalizujące (*kontrujące*). 
Typ `water` jest typem kontrującym typ `fire` i odwrotnie. 
Zwycięzcą takiej interakcji jest pokemon o większej sile. 

Wyszukuj przypadki, w których w ciągu 5 sekund zostały złapane pokemony 
o typach `fire` i `water`.

Wyniki powinny zawierać, następujące kolumny:
- `name_fire` - nazwa złapanego pokemona typu ognistego,
- `name_water` - nazwa złapanego pokemona wodnego, 
- `cp_fire` - siła złapanego pokemona typu ognistego,
- `cp_water` - siła złapanego pokemona typu wodnego,
- `winner` - zwycięzca interakcji 

W przypadku znalezienia wielu par kontrujących pokemonów należy 
zwrócić je wszystkie.

W przypadku równej siły obu pokemonów jako zwycięzcę podaj `draw`.

## Zadanie 6
Wyświetlaj informacje o przypadkach, gdy pojawią się pokemony 
w odpowiedniej kolejności, tworząc niebezpieczną synergię.
Taką kombinacją jest: pokemon typu `water`, następnie pokemon 
typu `electric` lub `poison`, a na koniec pokemon typu `flying`.
Aby ten ciąg wydarzeń rzeczywiście stał się tą niebezpieczną synergią, 
w czasie od złapania pokemona o typie `water` do momentu złapania 
pokemona o typie `flying` nie może zostać złapany pokemon o typie `rock`.

Wyniki powinny zawierać, następujące kolumny:
- `alert` - napis "CODE RED SCENARIO",
- `waterName` - nazwę złapanego pokemona typu `water`, 
- `startEts` - czas złapania pokemona typu `water`, 
- `secondName` - nazwę drugiego złapanego pokemona (typu `electric` lub `poison`),
- `flyingName` - nazwę złapanego pokemona typu `flying`,
- `stopEts` - czas złapania pokemona typu `flying`.

## Zadanie 7
Dla każdego typu pokemona chcemy wykrywać serie co najmniej dwóch 
przypadków złapania pokemona, w których każdy kolejny pokemon był 
silniejszy od pokemona złapanego wcześniej.
Każda seria kończy się w wyniku rejestracji złapania pokemona, 
którego siła wreszcie zmalała.    

Wyniki powinny zawierać, następujące kolumny:
- `min_power` - siła pierwszego pokemona w serii o rosnącej sile,
- `min_name` - nazwa pierwszego pokemona w serii o rosnącej sile,
- `start_ets` - czas złapania pierwszego pokemona w serii o rosnącej sile,
- `max_power` - siła ostatniego pokemona w serii o rosnącej sile,
- `max_name` - nazwa ostatniego pokemona w serii o rosnącej sile,
- `stop_ets` - czas złapania ostatniego pokemona w serii o rosnącej sile,
- `how_many` - liczba pokemonów w serii o rosnącej sile. 
