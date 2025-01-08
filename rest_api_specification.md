# Końcówki dla Rest API

## Końcówki do Autoryzacji

| Endpoint                                       | Metoda | Opis                                                                                        | Wymaga JWT |
| ---------------------------------------------- | ------ | ------------------------------------------------------------------------------------------- | ---------- |
| [**/api/auth/register**](#1-rejestracja)       | POST   | Umożliwia nowemu użytkownikowi zarejestrowanie się za pomocą loginu, adresu e-mail i hasła. | Nie        |
| [**/api/auth/login**](#2-logowanie)            | POST   | Uwierzytelnia użytkownika i zwraca token JWT do dalszych zapytań.                           | Nie        |
| [**/api/auth/logout**](#3-wylogowanie)         | POST   | Unieważnia token JWT, aby wylogować użytkownika.                                            | Tak        |
| [**/api/auth/refresh**](#4-odświeżenie-tokenu) | POST   | Zwraca nowy token JWT, jeśli stary wygasł, ale nadal jest ważny.                            | Tak        |

## Końcówki dla użytkownika

| Endpoint                                                             | Metoda | Opis                                                                                 | Wymaga JWT |
| -------------------------------------------------------------------- | ------ | ------------------------------------------------------------------------------------ | ---------- |
| [**/api/users/:id**](#1-pobierz-profil-użytkownika)                  | GET    | Pobiera profil użytkownika na podstawie podanego ID.                                 | Nie        |
| [**/api/users/profile**](#2-pobierz-profil-zalogowanego-użytkownika) | GET    | Pobiera profil aktualnie zalogowanego użytkownika (wymaga JWT).                      | Tak        |
| [**/api/users/profile**](#3-zaktualizuj-profil-użytkownika)          | PUT    | Aktualizuje profil aktualnie zalogowanego użytkownika.                               | Tak        |
| [**/api/users**](#4-pobierz-wszystkich-użytkowników)                 | GET    | Pobiera listę wszystkich użytkowników                                                | Nie        |
| [**/api/users/:id**](#5-usuń-użytkownika)                            | DELETE | Usuwa użytkownika o podanym ID (wymaga uprawnień administratora lub ważnego tokena). | Tak        |

## Końcówki scenariuszy

| Endpoint                                               | Metoda | Opis                                                               | Wymaga JWT |
| ------------------------------------------------------ | ------ | ------------------------------------------------------------------ | ---------- |
| [**/api/scenarios**](#1-pobierz-wszystkie-scenariusze) | GET    | Pobiera listę wszystkich dostępnych scenariuszy                    | Nie        |
| [**/api/scenarios/:id**](#2-pobierz-scenariusz-po-id)  | GET    | Pobiera szczegółowe informacje o scenariuszu na podstawie jego ID. | Nie        |
| [**/api/scenarios**](#3-stwórz-scenariusz)             | POST   | Tworzy nowy scenariusz na podstawie dostarczonych danych.          | Tak        |
| [**/api/scenarios/:id**](#4-zaktualizuj-scenariusz)    | PUT    | Aktualizuje istniejący scenariusz na podstawie jego ID.            | Tak        |
| [**/api/scenarios/:id**](#5-usuń-scenariusz)           | DELETE | Usuwa scenariusz na podstawie podanego ID.                         | Tak        |

## Końcówki gry

| Endpoint                                          | Metoda | Opis                                                                             | Wymaga JWT |
| ------------------------------------------------- | ------ | -------------------------------------------------------------------------------- | ---------- |
| [**/api/games**](#1-utwórz-grę)                   | POST   | Tworzy nową grę, wybiera scenariusz i inicjuje sesję początkową.                 | Tak        |
| [**/api/games/:id**](#2-pobierz-status-gry)       | GET    | Pobiera status i szczegóły gry na podstawie ID.                                  | Nie        |
| [**/api/games**](#3-pobierz-wszystkie-gry)        | GET    | Pobiera listę wszystkich gier.                                                   | Nie        |
| [**/api/games/:id/step**](#4-pobierz-krok-gry)    | GET    | Pobiera szczegóły bieżącego kroku gry na podstawie ID gry.                       | Nie        |
| [**/api/games/:id/step**](#5-aktualizuj-krok-gry) | POST   | Zaktualizowanie kroku w grze na podstawie ID gry i podjętej decyzji użytkownika. | Tak        |

## Końcówki do Autoryzacji

### 1. Rejestracja:

**POST /api/auth/register**  
 Pozwala nowemu użytkownikowi zarejestrować się za pomocą loginu, adresu e-mail i hasła.

Przykładowy payload:

```json
{
  "login": "exampleUser",
  "email": "example@email.com",
  "password": "examplePassword"
}
```

Odpowiedź:
Sukces (201 Created):

```json
{
  "message": "User registered successfully.",
  "user": {
    "id": 1,
    "login": "exampleUser",
    "email": "example@email.com",
    "createdAt": "2025-01-08T12:00:00Z"
  }
}
```

Błąd (400 Bad Request):

```json
{
  "error": "Validation failed.",
  "details": { "email": "Email is already in use." }
}
```

### 2. Logowanie:

**POST /api/auth/login**  
 Uwierzytelnia użytkownika i **zwraca** token JWT. Do wykorzystania w kolejnych zapytaniach.  
 Przykładowy payload:

```json
{
  "login": "exampleUser",
  "password": "examplePassword"
}
```

Odpowiedź
Sukces (200 OK):

```json
{
  "message": "Login successful.",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refreshToken": "dGhpc19pc19hX3JlZnJlc2hfdG9rZW4...",
  "user": { "id": 1, "login": "exampleUser", "email": "example@email.com" }
}
```

Błąd (401 Unauthorized):

```json
{ "error": "Invalid credentials." }
```

### 3. Wylogowanie:

    **POST /api/auth/logout**
    Unieważnia token JWT
    Payload:

Sukces (200 OK):

```json
{ "message": "Logout successful." }
```

Błąd (400 Bad Request):

```json
{ "error": "Invalid or missing refresh token." }
```

### 4. Odświeżenie Tokenu:

**POST /api/auth/refresh**  
 Zwraca nowy token JWT, jeśli poprzedni jest prawidłowy, ale wygasł

## Końcówki dla użytkownika

### 1. Pobierz profil użytkownika

**GET /api/users/:id**  
 Zwraca profil dowolnego użytkownika

Parametry w URL:  
 id: ID użytkownika (integer)

Kod odpowiedzi:

- 200 OK:  
  Profil użytkownika został pomyślnie pobrany.
- 404 Not Found:  
  Użytkownik o podanym ID nie istnieje.

Przykładowa odpowiedź (200 OK):

```json
{
  "id_user": 1,
  "login": "exampleUser",
  "email": "example@email.com"
}
```

### 2. Pobierz profil zalogowanego użytkownika

**GET /api/users/profile**  
 Zwraca profil aktualnie zalogowanego użytkownika (wymaga tokenu JWT)

Headers:

- Authorization: Bearer \<token\>

Parametry w URL:

- id: ID użytkownika (integer)

Kod odpowiedzi:

- 200 OK:  
  Prof Profil użytkownika został pomyślnie pobrany.il użytkownika został pomyślnie pobrany.
- 401 Unauthorized:  
  Brak tokenu lub token jest nieprawidłowy

Przykładowa odpowiedź (200 OK):

```json
{
  "id_user": 1,
  "login": "exampleUser",
  "email": "example@email.com"
}
```

### 3. Zaktualizuj profil użytkownika

**PUT /api/users/profile**  
 Aktualizuje profil zalogowanego użytkownika

Headers:

- Authorization: Bearer \<token\>

Kod odpowiedzi:

- 200 OK:  
  Prof Profil użytkownika został pomyślnie pobrany.il użytkownika został pomyślnie pobrany.
- 400 Bad Request:  
  Nieprawidłowe dane wejściowe.
- 401 Unauthorized:  
  Brak tokenu lub token jest nieprawidłowy

Przykładowy payload

```json
{
  "email": "newemail@email.com",
  "password": "newpassword123"
}
```

Przykładowa odpowiedź (200 OK):

```json
{
  "id_user": 1,
  "login": "exampleUser",
  "email": "newemail@email.com"
}
```

### 4. Pobierz wszystkich użytkowników

**GET /api/users**  
 Zwraca listę użytkowników z obsługą paginacji

Parametry Zapytania (Query Parameters)

- **page** (opcjonalne, domyślnie 1): Number strony
- **limit** (opcjonalne, domyślnie 10): Liczba wyników na stronę.

Kod odpowiedzi:

- 200 OK:  
  Lista użytkowników została pomyślnie pobrana.

Przykładowa odpowiedź (200 OK):

```json
{
  "page": 1,
  "limit": 10,
  "total": 50,
  "users": [
    { "id_user": 1, "login": "user1", "email": "user1@email.com" },
    { "id_user": 2, "login": "user2", "email": "user2@email.com" }
  ]
}
```

### 5. Usuń użytkownika

**DELETE /api/users/:id**  
 Usuwa użytkownika na podstawie podanego ID (tylko administratorzy lub jeżeli zgadza się token).

Parametry w URL

- **id**: ID użytkownika do usunięcia (integer).

Kod odpowiedzi:

- 200 OK:  
  Użytkownik został pomyślnie usunięty.
- 403 Forbidden:  
  Brak uprawnień do usunięcia użytkownika.

Przykładowa odpowiedź (200 OK):

```json
{
  "message": "User deleted successfully"
}
```

## Końcówki scenariuszy

### 1. Pobierz wszystkie scenariusze

**GET /api/scenarios**  
 Zwraca listę wszystkich dostępnych scenariuszy. Używa paginacji, aby zwrócić tylko część wyników.

Parametry zapytania:

**page** (integer) - Numer strony (domyślnie 1).  
 **limit** (integer) - Liczba scenariuszy na stronę (domyślnie 10).

Kody odpowiedzi:

- 200 OK - Zapytanie zakończone sukcesem, zwrócono dane.
- 400 Bad Request - Nieprawidłowe parametry zapytania (np. limit poza dozwolonym zakresem).

Odpowiedź: 200 OK

```json
{
  "data": [
    {
      "id": 1,
      "name": "Przygoda",
      "description": "Epicka podróż przez nieznane ziemie."
    },
    {
      "id": 2,
      "name": "Tajemnica",
      "description": "Rozwiąż emocjonującą zagadkę z każdą wskazówką."
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 50,
    "totalPages": 5
  }
}
```

### 2. Pobierz scenariusz po ID

**GET /api/scenarios/:id**  
 Zwraca szczegółowe informacje o scenariuszu na podstawie jego ID oraz jego 1 krok (korzeń drzewa).

Parametry:
id (integer) - ID scenariusza (wymagane).

Kody odpowiedzi:

- 200 OK - Scenariusz znaleziony, zwrócono szczegóły.
- 404 Not Found - Scenariusz o podanym ID nie istnieje.
- 400 Bad Request - Nieprawidłowy format ID.

Odpowiedź: 200 OK

```json
{
  "id": 1,
  "title": "Początek Przygody",
  "description": "Scenariusz, w którym gracz wyrusza na niezapomnianą podróż.",
  "firstStep": {
    "id": 1,
    "title": "Twoja podróż się zaczyna",
    "text": "Znajdujesz się na rozdrożu. Wybierz kierunek, w którym chcesz podążyć.",
    "choices": [
      { "id": 1, "text": "Idź w lewo" },
      { "id": 2, "text": "Idź w prawo" },
      { "id": 3, "text": "Wejdź na wzgórze" },
      { "id": 4, "text": "Wróć do domu" }
    ]
  }
}
```

### 3. Stwórz scenariusz

    **POST /api/scenarios**

Tworzy nowy scenariusz z podanymi danymi.

Kody odpowiedzi:

- 201 Created - Scenariusz został pomyślnie stworzony.
- 400 Bad Request - Brak wymaganych pól lub nieprawidłowe dane wejściowe.

Odpowiedź: 201 Created

```json
{
  "id": 3,
  "name": "Epicka Przygoda",
  "description": "Podróż pełna zwrotów akcji, w której każda decyzja ma znaczenie."
}
```

### 4. Zaktualizuj scenariusz

**PUT /api/scenarios/:id**  
 Aktualizuje istniejący scenariusz po jego ID.

Parametry:
id (integer) - ID scenariusza (wymagane).
Odpowiedź: 200 OK

```json
{
  "id": 1,
  "name": "Zaktualizowana Przygoda",
  "description": "Podróż pełna niebezpieczeństw i tajemnic, Twoje decyzje kształtują świat."
}
```

Kody odpowiedzi:

- 200 OK - Scenariusz został zaktualizowany.
- 400 Bad Request - Nieprawidłowy format danych.
- 404 Not Found - Scenariusz o podanym ID nie istnieje.

### 5. Usuń scenariusz

**DELETE /api/scenarios/:id**
Usuwa scenariusz na podstawie jego ID.
Parametry:

id (integer) - ID scenariusza (wymagane).
Odpowiedź: 200 OK

Kody odpowiedzi:

- 200 OK - Scenariusz został pomyślnie usunięty.
- 404 Not Found - Scenariusz o podanym ID nie istnieje.
- 400 Bad Request - Błędne ID scenariusza. 6. Pobierz kroki dla scenariusza

```json
{
  "message": "Scenariusz został pomyślnie usunięty."
}
```

## Końcówki gry

### 1. Utwórz grę:

**POST /api/games**  
Ten endpoint pozwala użytkownikowi stworzyć nową grę, wybrać scenariusz i tworzy nową sesję początkową.

Przykładowy payload:

```json
{
  "authorId": 1,
  "scenarioId": 1, // ID wybranego scenariusza
  "gameTitle": "My Adventure"
}
```

Kody odpowiedzi:

- **201 Created** - Gra została pomyślnie utworzona.
- **400 Bad Request** - Brak wymaganych danych lub nieprawidłowy format.

Response (201 Created):

```json
{
  "gameId": 1,
  "userId": 1,
  "scenarioId": 1,
  "currentStepId": 1,
  "status": "active"
}
```

### 2. Pobierz status gry:

**GET /api/games/:id**  
Ten punkt końcowy zwraca szczegóły i status danej gry.

Kody odpowiedzi:

- **200 OK** - Gra jest aktywna, zwrócono stan.
- **404 Not Found** - Gra o podanym ID nie istnieje.

Response (200 OK):

```json
{
  "gameId": 1,
  "userId": 1,
  "scenarioId": 1,
  "currentStepId": 2,
  "status": "active",
  "stepDetails": {
    "id": 2,
    "title": "In the Forest",
    "text": "You are standing at the edge of a dark forest.",
    "decisions": [
      {
        "id": 1,
        "title": "Enter the forest",
        "nextStepId": 3
      },
      {
        "id": 2,
        "title": "Turn back",
        "nextStepId": 4
      }
    ]
  }
}
```

### 3. Pobierz wszystkie gry:

**GET /api/games**  
Zwraca listę wszystkich gier (może być przydatne dla administratorów lub do pobrania historii gier).

Response (200 OK):

```json
[
  {
    "gameId": 1,
    "userId": 1,
    "scenarioId": 1,
    "currentStepId": 2,
    "status": "active"
  },
  {
    "gameId": 2,
    "userId": 2,
    "scenarioId": 2,
    "currentStepId": 5,
    "status": "completed"
  }
]
```

### 4. Pobierz krok gry:

**GET /api/games/:id/step**  
Pobiera szczegóły bieżącego kroku gry na podstawie ID gry.

Kody odpowiedzi:

- **200 OK** - Zwrócono szczegóły kroku gry.
- **404 Not Found** - Gra o podanym ID nie istnieje.

Response (200 OK):

```json
{
  "stepId": 2,
  "title": "In the Forest",
  "text": "You are standing at the edge of a dark forest.",
  "decisions": [
    {
      "id": 1,
      "title": "Enter the forest",
      "nextStepId": 3
    },
    {
      "id": 2,
      "title": "Turn back",
      "nextStepId": 4
    }
  ]
}
```

### 5. Aktualizuj krok gry:

**POST /api/games/:id/step**  
Aktualizuje krok w grze na podstawie ID gry.

Przykładowy payload:

```json
{
  "stepId": 2,
  "userDecision": 1 // ID podjętej decyzji przez użytkownika
}
```

Kody odpowiedzi:

- **200 OK** - Krok gry został pomyślnie zaktualizowany.
- **400 Bad Request** - Brak wymaganych danych lub nieprawidłowy format.

Response (200 OK):

```json
{
  "stepId": 3,
  "title": "Inside the Forest",
  "text": "You have entered the forest. It is dark and ominous.",
  "decisions": [
    {
      "id": 3,
      "title": "Proceed forward",
      "nextStepId": 5
    },
    {
      "id": 4,
      "title": "Look around",
      "nextStepId": 6
    }
  ]
}
```

## Końcówki sesji

- TODO:

---

## Przebieg Autoryzacji Użytkownika

Autoryzacja użytkownika obejmuje proces rejestracji, logowania, odświeżania tokenu JWT oraz uzyskiwania dostępu do zasobów za pomocą tokenu zapisanego w pamięci przeglądarki (local storage). Poniżej przedstawiono kroki autoryzacji z perspektywy użytkownika:

### 1. Rejestracja Użytkownika

**Endpoint:** `POST /api/auth/register`  
Użytkownik rejestruje się, podając login, adres e-mail i hasło.

**Przykładowy przebieg:**

1. Użytkownik wypełnia formularz rejestracji (login, e-mail, hasło).
2. Po przesłaniu formularza serwer zwraca potwierdzenie utworzenia konta.

**Przykładowa odpowiedź (201 Created):**

```json
{
  "message": "User registered successfully.",
  "user": {
    "id": 1,
    "login": "exampleUser",
    "email": "example@email.com",
    "createdAt": "2025-01-08T12:00:00Z"
  }
}
```

### 2. Logowanie Użytkownika

**Endpoint:** `POST /api/auth/login`  
Użytkownik loguje się, podając swój login i hasło. Po uwierzytelnieniu otrzymuje token JWT oraz refresh token.

Przykładowy przebieg:

Użytkownik wypełnia formularz logowania (login, hasło).
Serwer weryfikuje dane i zwraca token JWT, który użytkownik zapisuje w local storage.  
Przykładowa odpowiedź (200 OK):

```json
{
  "message": "Login successful.",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refreshToken": "dGhpc19pc19hX3JlZnJlc2hfdG9rZW4...",
  "user": {
    "id": 1,
    "login": "exampleUser",
    "email": "example@email.com"
  }
}
```

### 3. Odświeżenie Tokenu

**Endpoint:** `POST /api/auth/refresh`
Kiedy token JWT wygasa, użytkownik może za pomocą refresh tokenu uzyskać nowy token JWT.

Przykładowy przebieg:

Aplikacja zauważa, że token JWT wygasł.
Automatycznie wysyła refresh token do serwera, aby odnowić token JWT.
Nowy token JWT zostaje zapisany w local storage.
Przykładowa odpowiedź (200 OK):

```json
{
  "message": "Token refreshed successfully.",
  "token": "newly_generated_JWT_token",
  "refreshToken": "new_refresh_token"
}
```

### 4. Pobranie Profilu Użytkownika

**Endpoint:** `GET /api/users/profile`  
Użytkownik może pobrać swoje dane, korzystając z tokenu JWT przesłanego w nagłówku.

Przykładowy przebieg:

Aplikacja przesyła zapytanie do serwera z tokenem JWT w nagłówku.
Serwer weryfikuje token i zwraca dane zalogowanego użytkownika.
Przykładowa odpowiedź (200 OK):

```json
{
  "id_user": 1,
  "login": "exampleUser",
  "email": "example@email.com"
}
```

### 5. Otwieranie Aplikacji z Zapisanym Tokenem

Gdy użytkownik ponownie otwiera aplikację:

1. Aplikacja sprawdza, czy token JWT jest zapisany w local storage.
2. Jeśli token istnieje:
   - Próbuje pobrać profil użytkownika.
   - W przypadku wygaśnięcia tokenu JWT odświeża go za pomocą refresh tokenu.
   - TODO: (w przypadku wygaśniecia tokenu usuń go z local storage i po prostu pokaż ekran logowania)
3. Jeśli tokenu brak lub jest nieprawidłowy, użytkownik zostaje przekierowany na stronę logowania.

### Uwagi:

- Token JWT służy do autoryzacji przy każdym zapytaniu do zasobów chronionych.
- Refresh token jest używany tylko w przypadku wygaśnięcia tokenu JWT.
- Dane użytkownika nie są trwale przechowywane w aplikacji, a jedynie uzyskiwane w czasie rzeczywistym dzięki tokenom.

---

## Przebieg gry

1. **Logowanie użytkownika:**  
   **Endpoint:** `POST /api/auth/login`  
   Użytkownik loguje się, podając swoje dane uwierzytelniające. W odpowiedzi otrzymuje token JWT, który będzie używany do autoryzacji w kolejnych zapytaniach.

2. **Rozpoczęcie gry:**  
   **Endpoint:** `POST /api/games`  
   Użytkownik inicjuje nową grę. Tworzona jest nowa sesja gry powiązana z użytkownikiem oraz wybranym scenariuszem. Odpowiedź zawiera unikalne `id` nowej gry.

3. **Pobranie aktualnego kroku:**  
   **Endpoint:** `GET /api/games/:id/step`  
   Użytkownik przesyła zapytanie o aktualny krok scenariusza w kontekście konkretnej gry (z `id` gry w URL). W odpowiedzi otrzymuje szczegóły kroku, który użytkownik musi podjąć.  
   Odpowiedni krok dla gracza jest wybierany na podstawie jego userId zakodowanego w JWT

4. **Podejmowanie decyzji:**  
   **Endpoint:** `POST /api/games/:id/step`  
   Użytkownik przesyła swoją decyzję dotyczącą aktualnego kroku dla konkretnej gry. Na jej podstawie aplikacja przechodzi do następnego kroku w scenariuszu.

5. **Powtarzanie kroków 3-4:**  
   Powtarzaj sekwencję pobrania aktualnego kroku i podejmowania decyzji do momentu zakończenia gry lub przerwania sesji przez użytkownika.

---

## Wersje gry

### **Wersja 1.0**

- **Funkcjonalność:**
  - Pobieranie szczegółów aktualnego kroku gry przez endpoint `GET /api/games/:id/step`.
  - Podejmowanie decyzji dotyczących kroków gry przez endpoint `POST /api/games/:id/step`.
  - Wszystkie kroki są realizowane w pełni wirtualnie (brak integracji z GPS lub lokalizacją).

---

### **Wersja 2.0**

- **Funkcjonalność:**

  - Dodanie obsługi lokalizacji GPS.
  - Każdy krok w scenariuszu może wymagać odwiedzenia określonego miejsca.
  - Użytkownik musi wysłać swoją bieżącą lokalizację jako część decyzji.

- **Zmiany w endpointach:**

  - **Pobranie aktualnego kroku:**

    - Endpoint: `GET /api/games/:id/step`.
    - Odpowiedź zawiera również informacje o lokalizacji, do której użytkownik musi się udać, aby wykonać krok.

  - **Podejmowanie decyzji z lokalizacją:**

    - Endpoint: `POST /api/games/:id/step`.
    - Użytkownik wysyła decyzję oraz swoje bieżące współrzędne GPS.

    **Przykład payload:**

    ```json
    {
      "decision": "move forward",
      "location": {
        "latitude": 52.2297,
        "longitude": 21.0122
      }
    }
    ```

- **Walidacja lokalizacji:**
  - Serwer weryfikuje, czy użytkownik znajduje się w wymaganym promieniu od wskazanej lokalizacji przed zaakceptowaniem decyzji.

---

### **Wersja 3.0**

- **Funkcjonalność:**

  - Dodanie trybu multiplayer.
  - Każdy gracz w grze otrzymuje własne, unikalne decyzje w trakcie gry.
  - Decyzje są dostosowane indywidualnie na podstawie identyfikatora użytkownika zakodowanego w tokenie JWT.

- **Zmiany w endpointach:**

  - **Pobranie aktualnego kroku:**

    - Endpoint: `GET /api/games/:id/step`.
    - Odpowiedź zawiera krok gry specyficzny dla danego użytkownika na podstawie jego `userId` zakodowanego w JWT.

    **Przykład odpowiedzi dla gracza 1:**

    ```json
    {
      "step": "Find the hidden object near the fountain",
      "details": {
        "location": "Central Park, Fountain Area",
        "hint": "Look under the largest rock."
      }
    }
    ```

    **Przykład odpowiedzi dla gracza 2:**

    ```json
    {
      "step": "Solve the puzzle near the statue",
      "details": {
        "location": "Central Park, Statue Area",
        "hint": "Combine the symbols on the statue base."
      }
    }
    ```

- **Podejmowanie decyzji:**
  - Endpoint `POST /api/games/:id/step` działa w trybie multiplayer tak samo jak w poprzednich wersjach, ale serwer obsługuje równocześnie różne kroki dla różnych graczy w tej samej grze.

---

# Uwagi:

- Token JWT powinien być przesyłany w nagłówku `Authorization` w każdym zapytaniu.
- Decyzje użytkownika mogą zmieniać przebieg scenariusza gry w zależności od implementacji backendu.
- **PUT** jest odpowienikiem **GET**, więc przy aktualizacji, powinien on wstawiać całkowicie świeży zasób, nie aktualizaowac jego część.

# Do rozważenia

1. Przy DELETE zasobu, według mnie w przypadku nie znalezienia, powinniśmy zwracać 200, ponieważ w ostateczności osiągneliśmy cel i zasobu nie ma na serwerze.
2. Jak określić błędy i wiadomości czy zakodować to jako

```json
{
  "message": "Resource was deleted"
}
```

lub

```json
{
  "error": "Internal server error"
}
```

3. Czy w JWT kodowac id użytkownika czy id sesji, jak podtrzymać sesje na mobilce, dekodować token czy po wyjsciu zapisywać id do local storage
