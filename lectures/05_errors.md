# 🚨 Obsługa błędów i wyjątków w kontrolerach Spring

> **Jak zwracać poprawne kody HTTP i spójne komunikaty błędów w REST API**

---

## 🎯 Cel tego rozdziału

Po tym materiale:

✅ zrozumiesz różnicę między **błędem** a **wyjątkiem** w aplikacji

✅ nauczysz się mapować wyjątki na **kody HTTP (4xx/5xx)**

✅ poznasz podejścia: `ResponseEntity`, `@ResponseStatus`, `@ExceptionHandler`

✅ zobaczysz jak zrobić **globalną obsługę błędów** przez `@ControllerAdvice`

✅ dowiesz się, jak projektować **spójny format odpowiedzi błędu**

---

## 🧠 1. Błąd vs wyjątek – co to znaczy?

* **Wyjątek (Exception)** to zdarzenie w kodzie Javy, które przerywa normalny przepływ (np. `NullPointerException`, `IllegalArgumentException`, własny `NotFoundException`).
* **Błąd HTTP** to informacja zwracana klientowi w odpowiedzi (status + body), np. `404 Not Found`.

> 📌 W REST API wyjątek w kodzie *powinien zamienić się* w sensowną odpowiedź HTTP.

---

## 🧭 2. Kody HTTP a obsługa błędów

W praktyce:

### ✅ Najczęstsze kody 4xx (błędy klienta)

* **400 Bad Request** – niepoprawne dane wejściowe (zły format, brak pola, błędny typ)
* **401 Unauthorized** – brak uwierzytelnienia
* **403 Forbidden** – brak uprawnień
* **404 Not Found** – zasób nie istnieje (np. `Person` o danym ID)
* **409 Conflict** – konflikt stanu (np. próba utworzenia duplikatu)

### 🧯 Najczęstsze kody 5xx (błędy serwera)

* **500 Internal Server Error** – nieobsłużony wyjątek / błąd po stronie serwera

> 💡 Dobra praktyka: 4xx = klient może poprawić żądanie, 5xx = problem po stronie serwera.

---

## 🧩 3. Podejście 1: `ResponseEntity` (pełna kontrola)

`ResponseEntity` pozwala ustawić:

* status HTTP,
* nagłówki,
* body.

Koncepcyjny przykład dla braku zasobu:

```java
@GetMapping("/{id}")
public ResponseEntity<PersonDto> getPerson(@PathVariable Long id) {
    return personService.findById(id)
            .map(ResponseEntity::ok)
            .orElseGet(() -> ResponseEntity.notFound().build());
}
```

✅ Plusy:

* pełna kontrola w każdej metodzie.

⚠️ Minusy:

* łatwo powielać logikę (dużo „klepania” w wielu endpointach).

---

## 🏷️ 4. Podejście 2: `@ResponseStatus` na wyjątku

Możesz stworzyć własny wyjątek i przypisać mu status HTTP.

```java
@ResponseStatus(HttpStatus.NOT_FOUND)
public class PersonNotFoundException extends RuntimeException {
    public PersonNotFoundException(Long id) {
        super("Person not found: " + id);
    }
}
```

W kontrolerze/serwisie rzucasz wyjątek:

```java
throw new PersonNotFoundException(id);
```

✅ Plusy:

* proste,
* czytelne,
* Spring automatycznie zwróci właściwy status.

⚠️ Minusy:

* trudniej kontrolować format body błędu (często chcesz jednolity JSON).

---

## 🧰 5. Podejście 3: `@ExceptionHandler` w kontrolerze

Możesz złapać wyjątek w tym samym kontrolerze:

```java
@ExceptionHandler(PersonNotFoundException.class)
public ResponseEntity<ErrorResponse> handleNotFound(PersonNotFoundException ex) {
    ErrorResponse body = new ErrorResponse("NOT_FOUND", ex.getMessage());
    return ResponseEntity.status(HttpStatus.NOT_FOUND).body(body);
}
```

✅ Plusy:

* kontrolujesz status i treść odpowiedzi,
* nie mieszasz kodu błędów w każdej metodzie endpointu.

⚠️ Minusy:

* obsługa jest lokalna (tylko dla jednego kontrolera).

---

## 🌍 6. Globalna obsługa błędów: `@ControllerAdvice`

Najczęściej w realnych projektach robi się **globalny handler**.

Idea:

* w jednym miejscu przechwytujesz wyjątki,
* mapujesz je na statusy,
* zwracasz spójne body błędu.

Przykład:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(PersonNotFoundException.class)
    public ResponseEntity<ErrorResponse> handlePersonNotFound(PersonNotFoundException ex) {
        ErrorResponse body = new ErrorResponse("NOT_FOUND", ex.getMessage());
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(body);
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidation(MethodArgumentNotValidException ex) {
        ErrorResponse body = new ErrorResponse("VALIDATION_ERROR", "Invalid request body");
        return ResponseEntity.badRequest().body(body);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGeneric(Exception ex) {
        ErrorResponse body = new ErrorResponse("INTERNAL_ERROR", "Unexpected error");
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(body);
    }
}
```

> 📌 `@RestControllerAdvice` = `@ControllerAdvice` + automatyczna serializacja body do JSON.

---

## 🧾 7. Spójny format odpowiedzi błędu (ErrorResponse)

Dobra praktyka: zwracać błąd w spójnym formacie.

Przykładowe pola:

* 🕒 `timestamp`
* 🔢 `status`
* 🏷️ `error`
* 💬 `message`
* 📍 `path`

Minimalna wersja (koncepcyjnie):

```java
public class ErrorResponse {
    private String code;
    private String message;

    public ErrorResponse(String code, String message) {
        this.code = code;
        this.message = message;
    }

    public String getCode() { return code; }
    public String getMessage() { return message; }
}
```

> 💡 Format błędu to część kontraktu API — frontend lub integracje będą na nim polegały.

---

## ✅ 8. Jakie błędy warto obsłużyć w CRUD dla `Person`?

Dla `PersonController` typowe przypadki:

* `PersonNotFoundException` → **404**
* walidacja DTO (np. brak pola email) → **400**
* konflikt (np. email musi być unikalny) → **409**
* nieobsłużony wyjątek → **500**

---

## ⚠️ 9. Typowe pułapki

* ❌ zwracanie zawsze `200 OK` z komunikatem „error” w body
* ❌ brak spójnego formatu błędu (każdy endpoint inaczej)
* ❌ ujawnianie stack trace / szczegółów bazy danych w odpowiedzi
* ❌ brak rozróżnienia 4xx vs 5xx

✅ dobra praktyka:

* stosuj poprawne statusy,
* zwracaj spójne body błędu,
* loguj szczegóły po stronie serwera.

---

