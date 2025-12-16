# 🧭 Kontroler i endpointy w Spring

> **Wprowadzenie do warstwy webowej aplikacji Spring / REST API**

---

## 🎯 Cel tego rozdziału

Celem tego materiału jest:

✅ zrozumienie **roli kontrolera** w aplikacji Spring

✅ poznanie pojęcia **endpointu**

✅ umiejscowienie kontrolera w architekturze aplikacji webowej


---

## 🧩 1. Czym jest kontroler?

### 🏗️ Kontroler w architekturze aplikacji

W aplikacjach opartych o **Spring MVC / Spring Boot** kontroler jest elementem **warstwy webowej (prezentacji)**.

Najczęściej spotykany podział warstw:

* 🧭 **Controller** – obsługa żądań HTTP (wejście do aplikacji)
* ⚙️ **Service** – logika biznesowa
* 🗄️ **Repository** – dostęp do bazy danych

---

### 📥 Co robi kontroler?

Kontroler:

* 📡 odbiera żądanie HTTP (np. z przeglądarki, Postmana, frontendu),
* 🧠 decyduje, **która metoda ma zostać wywołana**,
* 🔁 przekazuje dane dalej (najczęściej do warstwy serwisowej),
* 📤 zwraca odpowiedź HTTP do klienta.

---

### 🚫 Czego kontroler NIE powinien robić?

Kontroler **nie powinien**:

* ❌ zawierać logiki biznesowej,
* ❌ bezpośrednio operować na bazie danych,
* ❌ podejmować złożonych decyzji domenowych.

> 💡 Kontroler ma być **lekki i czytelny** – pełni rolę „bramy” do aplikacji.

---

## 🌐 2. Kontroler a REST API

W nowoczesnych aplikacjach backendowych kontroler bardzo często pełni rolę **REST kontrolera**.

### 🔗 Cechy REST kontrolera

REST kontroler:

* 🌍 komunikuje się za pomocą protokołu **HTTP**,
* 📦 operuje na **zasobach** (np. `Person`),
* 🔧 używa standardowych metod HTTP (`GET`, `POST`, `PUT`, `DELETE`),
* 🧾 zwraca dane najczęściej w formacie **JSON**.

---

### 🧠 Przykład myślowy

* zasób: 👤 **Person**
* kontroler: 🧭 **PersonController**
* API umożliwia:

  * pobieranie osób,
  * dodawanie nowych,
  * edycję istniejących,
  * usuwanie.

---

## 🎯 3. Czym jest endpoint?

### 📍 Definicja endpointu

**Endpoint** to:

> 🔹 **konkretny adres URL + metoda HTTP**, które prowadzą do konkretnej metody w kontrolerze

Innymi słowy:

* endpoint = 🚪 „punkt wejścia” do aplikacji przez HTTP

---

### 📌 Przykładowe endpointy (koncepcyjnie)

* `GET /api/persons` → 📃 pobranie listy osób
* `GET /api/persons/1` → 👤 pobranie jednej osoby
* `POST /api/persons` → ➕ utworzenie nowej osoby
* `PUT /api/persons/1` → ✏️ aktualizacja osoby
* `DELETE /api/persons/1` → 🗑️ usunięcie osoby

Każdy endpoint:

* 🔗 jest mapowany na **jedną metodę w kontrolerze**,
* 🎯 ma jasno określoną odpowiedzialność.

---

## 🔄 4. Kontroler jako punkt wejścia do aplikacji

### 🛣️ Schemat przepływu żądania

1. 🧑‍💻 Klient wysyła żądanie HTTP
2. 🧩 Spring analizuje:

   * adres URL
   * metodę HTTP
3. 🧭 Spring wybiera **odpowiedni kontroler i metodę**
4. 🧱 Dane wejściowe są mapowane na obiekty Javy
5. ▶️ Metoda kontrolera jest wywoływana
6. 📤 Odpowiedź jest zamieniana na HTTP response (np. JSON)

> 📌 Kontroler jest **pierwszym miejscem w kodzie**, które obsługuje żądanie z zewnątrz.

---

## 🏷️ 5. Adnotacje kontrolera – idea (bez szczegółów)

Spring rozpoznaje kontrolery i endpointy dzięki **adnotacjom**.

Na tym etapie zapamiętaj:

* 🏷️ adnotacja może oznaczać klasę jako kontroler,
* 🧭 adnotacje mówią Springowi:

  * jaki URL obsługuje dana klasa,
  * jakie URL-e obsługują konkretne metody,
  * w jaki sposób dane są przesyłane.

📘 Szczegółowe omówienie adnotacji i metod HTTP pojawi się w **kolejnym pliku**.

---

## 👤 6. Powiązanie z encją `Person`

W naszym przypadku:

* 🗄️ encja JPA: `Person`
* 🧭 kontroler: `PersonController`
* 🔄 kontroler **nie pracuje bezpośrednio na encji**, lecz na `PersonDto`

### 🔐 Dlaczego to ważne?

To rozdzielenie:

* 🔒 chroni model bazy danych,
* 🔄 ułatwia wersjonowanie API,
* 🧨 zapobiega niekontrolowanemu wystawianiu relacji (`User`, `Address`, `Picture`).

---
