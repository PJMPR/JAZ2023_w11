# 🌐 Metody HTTP, URL i adnotacje w Spring

> **Jak żądanie HTTP trafia do konkretnej metody kontrolera**

---

## 🎯 Cel tego rozdziału

Po tym materiale:

✅ zrozumiesz, czym są **metody HTTP** i do czego służą

✅ dowiesz się, jak interpretować **adres URL** w REST API

✅ poznasz ideę mapowania endpointów w Spring

✅ zrozumiesz rolę najważniejszych **adnotacji kontrolera**

Nie skupiamy się jeszcze na pełnym CRUD — budujemy fundamenty pod jego implementację.

---

## 🌍 1. Protokół HTTP – krótki kontekst

HTTP (**HyperText Transfer Protocol**) to protokół komunikacyjny używany w aplikacjach webowych.

Charakterystyka HTTP:

* 📡 komunikacja klient ↔ serwer,
* 🔁 model request → response,
* 🧠 bezstanowość (stateless),
* 🌐 uniwersalność (przeglądarka, Postman, frontend).

> 📌 Każda interakcja z kontrolerem Spring odbywa się przez HTTP.

---

## 🔧 2. Metody HTTP (czasowniki)

Metoda HTTP **opisuje intencję operacji**, jaką klient chce wykonać na zasobie.

### 📘 Najważniejsze metody HTTP

| Metoda | Znaczenie              | Przykładowe użycie |
| ------ | ---------------------- | ------------------ |
| GET    | Pobranie danych        | pobranie osoby     |
| POST   | Utworzenie zasobu      | dodanie osoby      |
| PUT    | Aktualizacja całości   | edycja osoby       |
| PATCH  | Aktualizacja częściowa | zmiana emaila      |
| DELETE | Usunięcie zasobu       | usunięcie osoby    |

> 🧠 Metoda HTTP **nie opisuje adresu**, tylko *co chcemy zrobić*.

---

## 🧭 3. Adres URL w REST API

### 📍 Struktura URL

Adres URL w REST API opisuje **zasób**, a nie akcję.

Przykład:

```
/api/persons/1
```

Interpretacja:

* `/api` – kontekst API
* `/persons` – kolekcja zasobów
* `/1` – identyfikator konkretnego zasobu

> ❗ W REST **nie używamy czasowników w URL** (np. `/getPerson`).

---

## 🎯 4. Endpoint = URL + metoda HTTP

Ten sam URL może oznaczać **coś zupełnie innego** w zależności od metody HTTP.

Przykład logiczny:

| Metoda | URL            | Znaczenie        |
| ------ | -------------- | ---------------- |
| GET    | /api/persons   | pobranie listy   |
| POST   | /api/persons   | utworzenie nowej |
| GET    | /api/persons/1 | pobranie jednej  |
| PUT    | /api/persons/1 | aktualizacja     |
| DELETE | /api/persons/1 | usunięcie        |

> 📌 Spring wybiera metodę kontrolera na podstawie **obu elementów jednocześnie**.

---

## 🏷️ 5. Mapowanie endpointów w Spring

Spring używa **adnotacji**, aby powiązać:

* metodę HTTP,
* adres URL,
* metodę w kontrolerze.

### 🧭 Adnotacja klasy kontrolera

Kontroler REST oznaczamy adnotacją:

* `@RestController`

Znaczenie koncepcyjne:

* klasa obsługuje żądania HTTP,
* odpowiedzi są automatycznie serializowane (np. do JSON).

---

### 📍 Bazowy adres URL

Do zdefiniowania wspólnego prefiksu URL używa się:

* `@RequestMapping`

Przykładowa idea:

* klasa obsługuje wszystkie endpointy zaczynające się od `/api/persons`

> 📌 Dzięki temu nie powtarzamy adresu w każdej metodzie.

---

## 🔗 6. Adnotacje mapujące metody HTTP

Spring udostępnia dedykowane adnotacje dla każdej metody HTTP:

* `@GetMapping` – obsługa GET
* `@PostMapping` – obsługa POST
* `@PutMapping` – obsługa PUT
* `@PatchMapping` – obsługa PATCH
* `@DeleteMapping` – obsługa DELETE

Każda z nich:

* określa metodę HTTP,
* może zawierać fragment URL,
* mapuje endpoint na **jedną metodę Javy**.

---

## 🧠 7. Jak Spring wybiera metodę kontrolera?

Spring analizuje żądanie w następującej kolejności:

1. 🧭 adres URL
2. 🔧 metodę HTTP
3. 🏷️ adnotacje na klasie i metodach

Jeśli znajdzie dokładne dopasowanie → metoda zostaje wywołana.

Jeśli nie:

* ❌ zwracany jest błąd (np. 404 lub 405).

---

## 👤 8. Odniesienie do `PersonController`

Dla zasobu `Person`:

* bazowy URL: `/api/persons`
* różne metody HTTP oznaczają różne operacje
* każda metoda kontrolera odpowiada **jednemu przypadkowi użycia**

> 🎯 Kontroler nie "robi CRUD" — on **udostępnia kontrakt HTTP** do wykonania CRUD.


**obsługi nagłówków HTTP oraz przekazywania danych w żądaniu i odpowiedzi** 📦
