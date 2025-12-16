# 📦 Obsługa nagłówków HTTP w Spring

> **Jak kontroler wpływa na nagłówki odpowiedzi HTTP**

---

## 🎯 Cel tego rozdziału

Po tym materiale:

✅ zrozumiesz, czym są **nagłówki HTTP** i do czego służą

✅ poznasz różnicę między nagłówkami żądania i odpowiedzi

✅ dowiesz się, kiedy i dlaczego backend ustawia nagłówki

✅ zobaczysz **praktyczny przykład** nagłówka `Location` w metodzie `POST`

---

## 🌐 1. Czym są nagłówki HTTP?

Nagłówki HTTP to **metadane** przesyłane razem z żądaniem lub odpowiedzią HTTP.

Nie są one właściwą treścią (body), lecz opisują:

* 📌 sposób interpretacji danych,
* 🔐 informacje techniczne i kontekstowe,
* 🔄 zasady komunikacji klient–serwer.

---

## 📥 2. Nagłówki żądania (Request Headers)

Nagłówki żądania są wysyłane przez **klienta** do serwera.

Przykładowe zastosowania:

* `Content-Type` – format danych wysyłanych w body (np. JSON),
* `Accept` – format odpowiedzi, jakiego oczekuje klient,
* `Authorization` – dane uwierzytelniające,
* `User-Agent` – informacja o kliencie.

> 📌 Spring potrafi automatycznie reagować na wiele nagłówków (np. `Content-Type`).

---

## 📤 3. Nagłówki odpowiedzi (Response Headers)

Nagłówki odpowiedzi są ustawiane przez **serwer** i wysyłane do klienta.

Służą m.in. do:

* 📍 przekazywania informacji o nowo utworzonym zasobie,
* ⏱️ kontroli cache,
* 🔐 polityk bezpieczeństwa,
* 📄 opisu formatu odpowiedzi.

Jednym z najważniejszych nagłówków w REST API jest **`Location`**.

---

## 📍 4. Nagłówek `Location` – idea REST

Nagłówek **`Location`**:

* wskazuje **adres URL nowo utworzonego zasobu**,
* jest standardem REST przy operacjach `POST`,
* najczęściej występuje razem ze statusem **201 Created**.

### 🧠 Dlaczego to ważne?

Po utworzeniu zasobu klient:

* wie, **gdzie znajduje się nowy obiekt**,
* może od razu wykonać `GET` pod wskazanym adresem,
* nie musi zgadywać struktury URL.

---

## 🧭 5. Status HTTP a nagłówki

Tworząc nowy zasób:

* ✅ status: **201 Created**
* 📍 nagłówek: **Location**
* 📦 body: opcjonalnie (np. DTO)

> 📌 To połączenie jest uznawane za **dobrą praktykę REST**.

---

## 🧪 6. Przykład: metoda POST z nagłówkiem Location

Poniższy przykład pokazuje **ideę**, a nie pełną implementację CRUD.

```java
@PostMapping
public ResponseEntity<PersonDto> createPerson(@RequestBody PersonDto dto) {

    // 1. Zapis osoby (np. przez serwis)
    PersonDto savedPerson = personService.create(dto);

    // 2. Zbudowanie URL do nowo utworzonego zasobu
    URI location = URI.create("/api/persons/" + savedPerson.getId());

    // 3. Zwrócenie odpowiedzi z nagłówkiem Location
    return ResponseEntity
            .created(location)   // status 201 + Location
            .body(savedPerson);
}
```

---

## 🔍 7. Co tu się dzieje krok po kroku?

1. 📥 Klient wysyła `POST /api/persons` z danymi osoby
2. 🧠 Backend zapisuje dane i otrzymuje **ID nowego zasobu**
3. 📍 Tworzony jest URL wskazujący na nową osobę
4. 📤 Serwer zwraca:

   * status `201 Created`,
   * nagłówek `Location: /api/persons/{id}`,
   * body z reprezentacją obiektu

---

## 🛠️ 8. Dlaczego używamy `ResponseEntity`?

`ResponseEntity` pozwala kontrolerowi:

* ustawić **status HTTP**,
* dodać **nagłówki odpowiedzi**,
* kontrolować **ciało odpowiedzi**.

Daje to pełną kontrolę nad odpowiedzią HTTP — czego nie zapewnia samo zwracanie obiektu.

---

## 👤 9. Odniesienie do `PersonController`

Dla zasobu `Person`:

* `POST /api/persons` → tworzy nową osobę
* odpowiedź:

  * **201 Created**
  * nagłówek `Location`
  * `PersonDto` w body

> 🎯 Kontroler realizuje kontrakt HTTP — serwis realizuje logikę biznesową.

---
