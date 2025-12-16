# 🧩 Parametry ścieżki i parametry zapytania (Path & Query Params)

> **Jak przekazywać dane w URL i odbierać je w kontrolerze Spring**

---

## 🎯 Cel tego rozdziału

Po tym materiale:

✅ zrozumiesz różnicę między **parametrami ścieżki** a **parametrami zapytania**

✅ dowiesz się, kiedy używać każdego z nich

✅ poznasz kluczowe adnotacje Spring (`@PathVariable`, `@RequestParam`)

✅ zobaczysz **czytelne przykłady metod kontrolera**

---

## 🌐 1. Parametry w URL – kontekst REST

W REST API dane mogą być przekazywane:

* 📦 w **body** (np. JSON – głównie POST / PUT),
* 📍 w **ścieżce URL** (path parameters),
* 🔍 w **query string** (query parameters),
* 🏷️ w nagłówkach HTTP.

Ten rozdział skupia się na **danych przekazywanych w URL**.

---

## 📍 2. Parametry ścieżki (Path Parameters)

### 🧠 Czym są parametry ścieżki?

Parametry ścieżki:

* są **częścią adresu URL**,
* identyfikują **konkretny zasób**,
* są obowiązkowe.

Przykład URL:

```
GET /api/persons/5
```

Interpretacja:

* `/api/persons` → kolekcja
* `/5` → identyfikator konkretnej osoby

---

### 🏷️ Adnotacja `@PathVariable`

Spring mapuje fragment URL na parametr metody przy użyciu:

* `@PathVariable`

Przykład:

```java
@GetMapping("/{id}")
public PersonDto getPerson(@PathVariable Long id) {
    // id pochodzi bezpośrednio z URL
}
```

> 📌 Nazwa zmiennej w URL (`{id}`) musi odpowiadać nazwie parametru metody
> lub być jawnie wskazana.

---

### 🎯 Kiedy używać parametrów ścieżki?

Używaj **path parameters**, gdy:

* pracujesz na **konkretnym zasobie**,
* operacja dotyczy **jednego obiektu**,
* identyfikator jest częścią tożsamości zasobu.

Typowe przypadki:

* `GET /persons/{id}`
* `PUT /persons/{id}`
* `DELETE /persons/{id}`

---

## 🔍 3. Parametry zapytania (Query Parameters)

### 🧠 Czym są parametry query?

Parametry zapytania:

* występują po znaku `?` w URL,
* mają postać `klucz=wartość`,
* są **opcjonalne**,
* służą do **filtrowania, sortowania, stronicowania**.

Przykład:

```
GET /api/persons?gender=female&age=30
```

---

### 🏷️ Adnotacja `@RequestParam`

Spring mapuje parametry query przy użyciu:

* `@RequestParam`

Przykład:

```java
@GetMapping
public List<PersonDto> findPersons(
        @RequestParam String gender,
        @RequestParam Integer age
) {
    // gender i age pochodzą z query string
}
```

---

### ⚙️ Parametry opcjonalne

Parametry query bardzo często są **opcjonalne**.

Spring pozwala na:

```java
@RequestParam(required = false)
```

lub ustawienie wartości domyślnej:

```java
@RequestParam(defaultValue = "0") int page
```

---

## 🧭 4. Path vs Query – porównanie

| Cecha        | Path Parameter       | Query Parameter    |
| ------------ | -------------------- | ------------------ |
| Położenie    | część URL            | po `?`             |
| Obowiązkowy  | zazwyczaj tak        | często nie         |
| Zastosowanie | identyfikacja zasobu | filtrowanie, opcje |
| Przykład     | `/persons/1`         | `?page=0&size=10`  |

> 📌 **Path = co?**, **Query = jak? / z jakimi opcjami?**

---

## 👤 5. Przykłady dla `PersonController`

### 📍 Pobranie jednej osoby

```
GET /api/persons/10
```

→ `@PathVariable Long id`

---

### 🔍 Filtrowanie listy osób

```
GET /api/persons?gender=male&age=40
```

→ `@RequestParam gender`, `@RequestParam age`

---

## ⚠️ 6. Typowe błędy projektowe

❌ używanie query param do identyfikacji zasobu:

```
GET /api/persons?id=5
```

❌ umieszczanie akcji w URL:

```
GET /api/persons/getById/5
```

✅ poprawnie (REST):

```
GET /api/persons/5
```

---
