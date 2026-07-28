# Berlin — moja mapa

Prywatny planer wycieczki do Berlina. Jedna strona, zero buildu, zero backendu —
całość to `index.html` plus zdjęcia i zrzut linii metra.

**Na żywo:** https://ptkx.github.io/TripMaps/

## Co potrafi

- **Miejsca w kategoriach** — atrakcje must-see, kebaby, hotele, jedzenie, własne pinezki.
  Każde miejsce ma notatkę, którą można edytować w locie.
- **Linie U-Bahn** — rysowane z lokalnego `metro.json`; gdy go zabraknie, leci zapytanie
  na żywo do Overpass API.
- **Wyszukiwarka** miejsc w granicach Berlina (Nominatim / OpenStreetMap).
- **Optymalna trasa spacerowa** — zaznacz kilka punktów, a apka ułoży kolejność
  (nearest-neighbour + 2-opt) i otworzy gotową trasę pieszą w Google Maps.
- **Eksport / import** listy miejsc do pliku JSON.

## Gdzie siedzą dane

Wszystko trzyma się w `localStorage` przeglądarki pod kluczem `berlin-mapa-v1`.
Nic nie leci na żaden serwer — ale to też znaczy, że **stan nie synchronizuje się
między urządzeniami**. Telefon i laptop mają osobne listy. Przenoszenie odbywa się
ręcznie: ⭳ Eksport na jednym, ⭱ Import na drugim.

Zmiana adresu strony (inny host, inny port, `file://` zamiast `https://`) to dla
przeglądarki inny origin, więc zaczynasz z pustą listą. Eksportuj przed przeprowadzką.

## Uruchamianie lokalnie

Nie otwieraj `index.html` podwójnym kliknięciem. Przy `file://` przeglądarka nie
wysyła nagłówka `Referer`, a serwery kafelków OpenStreetMap odrzucają wtedy żądania
(dostaniesz kafelki „Access blocked"). Do tego `fetch("metro.json")` wpada na CORS.

Postaw serwer lokalny:

```
python -m http.server 8000
```

i wejdź na `http://localhost:8000`. Trzymaj się stale tego samego portu — inny port
to inny origin, czyli utrata zapisanych miejsc.

## Zewnętrzne usługi

Apka odpytuje publiczne, darmowe API na zasadach ich polityk użycia — wszystkie
mają limity i potrafią chwilowo odmówić:

| Usługa | Do czego | Uwagi |
|---|---|---|
| tile.openstreetmap.org | kafelki mapy | wymaga nagłówka `Referer` |
| nominatim.openstreetmap.org | wyszukiwarka miejsc | limit ~1 zapytanie/s |
| overpass-api.de | linie metra (fallback) | używane tylko gdy brak `metro.json` |

## Licencje

Mapa: dane © [OpenStreetMap](https://www.openstreetmap.org/copyright), licencja ODbL.

Zdjęcia w `img/` pochodzą z Wikimedia Commons i są używane na licencjach CC / GFDL —
autor i licencja każdego z nich są wypisane w [IMAGE_CREDITS.md](IMAGE_CREDITS.md)
oraz pokazywane w dymku pod zdjęciem.

## Znane ograniczenia

- Google Maps przyjmuje maksymalnie ~10 przystanków w trasie; nadmiarowe zaznaczenia
  są obcinane (apka o tym informuje).
- Ikona na ekranie głównym jest w SVG. Android to łyka, iOS przy „Dodaj do ekranu
  początkowego" może podstawić własny zrzut ekranu zamiast ikony.
- Kolejność trasy liczona jest po linii prostej (haversine), nie po realnej sieci
  ulic — dla spaceru po centrum wystarcza, ale nie jest to prawdziwy routing.
