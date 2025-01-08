# Końcówki dla Rest API

## Końcówki do Autoryzacji

- Rejestracja:  
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

- Logowanie:  
  **POST /api/auth/login**  
  Uwierzytelnia użytkownika i **zwraca** token JWT. Przykładowy payload:

```json
{
  "login": "exampleUser",
  "password": "examplePassword"
}
```

- Wylogowanie:  
  **POST /api/auth/logout**  
  Unieważnia token JWT

- Odświeżenie Tokenu:  
  **POST /api/auth/refresh**  
  Zwraca nowy token JWT, jeśli poprzedni jest prawidłowy, ale wygasł

## Końcówki dla użytkownika

- Pobierz profil użytkownika  
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

- Pobierz profil zalogowanego użytkownika  
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

- Zaktualizuj profil użytkownika
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

- Utwórz użytkownika  
  **POST /api/users**  
  Rejestruje nowego użytkownika.

  Kod odpowiedzi:

  - 201 Created:  
    Użytkownik został pomyślnie utworzony
  - 400 Bad Request:  
    Nieprawidłowe dane wejściowe. (np. login, email już są w użyciu, błąd walidacji hasła)

Przykładowy payload

```json
{
  "login": "newUser",
  "email": "newuser@email.com",
  "password": "securepassword123"
}
```

Przykładowa odpowiedź (201 Created):

```json
{
  "id_user": 2,
  "login": "newUser",
  "email": "newuser@email.com"
}
```

- Pobierz wszystkich użytkoników (stronnicowanie/pagination)  
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

- Usuń użytkownika
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

- Pobierz wszystkie scenariusze  
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

- Pobierz scenariusz po ID  
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

- Stwórz scenariusz  
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

- Zaktualizuj scenariusz  
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

200 OK - Scenariusz został zaktualizowany.
400 Bad Request - Nieprawidłowy format danych.
404 Not Found - Scenariusz o podanym ID nie istnieje.
422 Unprocessable Entity - Błędy walidacji danych.

- Usuń scenariusz  
  **DELETE /api/scenarios/:id**
  Usuwa scenariusz na podstawie jego ID.
  Parametry:

  id (integer) - ID scenariusza (wymagane).
  Odpowiedź: 200 OK

  Kody odpowiedzi:

  200 OK - Scenariusz został pomyślnie usunięty.
  404 Not Found - Scenariusz o podanym ID nie istnieje.
  400 Bad Request - Błędne ID scenariusza. 6. Pobierz kroki dla scenariusza

```json
{
  "message": "Scenariusz został pomyślnie usunięty."
}
```

- Pobierz wszystkie kroki scenariusza  
  **GET /api/scenarios/:id/steps**  
  Zwraca wszystkie kroki związane z danym scenariuszem. Używa paginacji, aby zwrócić tylko część wyników.

Parametry:
id (integer) - ID scenariusza, dla którego pobieramy kroki.
page (integer) - Numer strony (domyślnie 1).
limit (integer) - Liczba kroków na stronę (domyślnie 10).
Odpowiedź: 200 OK

Kody odpowiedzi:

- 200 OK - Zapytanie zakończone sukcesem, zwrócono kroki.
- 400 Bad Request - Nieprawidłowe parametry zapytania.
- 404 Not Found - Scenariusz o podanym ID nie istnieje.

```json
{
  "data": [
    {
      "id": 1,
      "title": "Zaczynasz swoją podróż",
      "text": "Jesteś na skraju ogromnego lasu."
    },
    {
      "id": 2,
      "title": "Przekrocz rzekę",
      "text": "Szybko płynąca rzeka blokuje Twoją drogę."
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 30,
    "totalPages": 3
  }
}
```

- Stwórz krok w scenariuszu
  **POST /api/scenarios/:id/steps**
  Tworzy nowy krok w ramach scenariusza.

Parametry:

id (integer) - ID scenariusza, do którego dodajemy krok.
Kody odpowiedzi:

201 Created - Krok został pomyślnie stworzony.
400 Bad Request - Brak wymaganych pól lub nieprawidłowe dane.

```json
{
  "id": 3,
  "title": "Wspinaj się na górę",
  "text": "Przed Tobą stroma góra. Czy spróbujesz się na nią wspiąć?"
}
```

- Zaktualizuj krok w scenariuszu  
  **PUT /api/scenarios/:id/steps/:id_step**  
  Aktualizuje istniejący krok w scenariuszu.

  Parametry:
  id (integer) - ID scenariusza, do którego należy krok.
  id_step (integer) - ID kroku do zaktualizowania.

  Kody odpowiedzi:

  - 200 OK - Krok został zaktualizowany.
  - 400 Bad Request - Błędne dane wejściowe.
  - 404 Not Found - Krok o podanym ID nie istnieje.

Odpowiedź: 200 OK

```json
{
  "id": 3,
  "title": "Wspinaj się na wysoką górę",
  "text": "Góra stała się jeszcze bardziej stroma. Czy uda Ci się dotrzeć na szczyt?"
}
```

9. Usuń krok w scenariuszu  
   **DELETE /api/scenarios/:id/steps/:id**  
   Usuwa krok w scenariuszu.

Parametry:  
 id (integer) - ID scenariusza, do którego należy krok.
id_step (integer) - ID kroku do usunięcia.
Odpowiedź: 200 OK

Kody odpowiedzi:

- 200 OK - Krok został usunięty.
- 404 Not Found - Krok o podanym ID nie istnieje.

```json
{
  "message": "Krok został pomyślnie usunięty."
}
```

---

## TODO: Końcówki kroków

- Pobierz aktualny krok:
  **GET /api/steps/current**
  Zwraca aktualny krok dla aktywnej sesji zalogowanego użytkownika.

-Podejmij decyzję:
**POST /api/steps/decision**
Przyjmuje decyzję użytkownika i przechodzi do następnego kroku.
Przykładowy payload:

```json
{
  "id_choice": 2
}
```

## Końcówki gry

- Utwórz grę:  
  **POST /api/games**  
  Ten endpoint pozwala użytkownikowi stworzyć nową grę, wybrać scenariusz i tworzy nową sesję początkową.
  payload:

  Kody odpowiedzi:

  - 201 Created - Gra została pomyślnie utworzona.
  - 400 Bad Request - Brak wymaganych danych lub nieprawidłowy format.

```json
{
TODO: powininen też zawierac id uzytkownika bioracego udzial w grze
  "scenarioId": 1, // ID wybranego scenariusza
  "gameTitle": "My Adventure"
}
```

Response (201 Created)

```json
{
  "gameId": 1,
  "userId": 1,
  "scenarioId": 1,
  "currentStepId": 1,
  "status": "active"
}
```

- Pobierz status gry:  
  **GET /api/games/id**  
  Ten punkt końcowy zwraca szczegóły i status danej gry.

  Kody odpowiedzi:

  - 200 OK - Gra jest aktywna, zwrócono stan.
  - 404 Not Found - Gra o podanym ID nie istnieje.

Response (200 OK)

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

- Utworzenie sesji gry
  **POST /api/games/:id_game/sessions**
  Tworzy nową sesję w obrębie danej gry.

  Kody odpowiedzi:
  - 201 Created - Sesja została pomyślnie utworzona.
  - 400 Bad Request - Brak danych użytkownika lub ID gry.

```json
{
  "user_id": 1 // ID użytkownika, który zaczyna sesję w grze
}
```


Response: (201 Created)  
Sesja zostaje utworzona i przypisana do gry.
```json
{
  "id_session": 1,
  "id_game": 1,
  "status": "started",
  "current_step_id": 1  // ID pierwszego kroku w scenariuszu
}
```

- TODO: Pobierz wszystkie gry:
  **GET /api/games**
  Zwraca listę wszystkich gier (może być przydatne dla administratorów lub do pobrania historii gier).

## TODO: Końcówki sesji

- Rozpocznij sesję:
  **POST /api/sessions**
  Tworzy sesję dla użytkownika i przypisuje ją do gry.

- Pobierz aktywną sesję:
  **GET /api/sessions/active**
  Zwraca aktualną aktywną sesję zalogowanego użytkownika.

- Zakończ sesję:
  **PUT /api/sessions/:id_ses/end**
  Kończy sesję po zakończeniu gry przez użytkownika.

## Końcówki decyzji

- Podejmowanie decyzji
  **POST /api/games/:gameId/decisions**
  Pozwala użytkownikowi podjąć decyzję i przejść do następnego kroku w grze na podstawie dokonanej decyzji.


  Kody odpowiedzi:
  - 200 OK - Decyzja została podjęta, użytkownik przeszedł do kolejnego kroku.
  - 400 Bad Request - Nieprawidłowy wybór decyzji.

Payload
```json
{
  "decisionId": 1 // ID podjętej decyzji
}
````

Response (200 OK):

```json
{
  "gameId": 1,
  "userId": 1,
  "currentStepId": 4,
  "status": "active"
}
```

## Przykładowy przebieg gry

1. Logowanie użytkownika: POST /api/auth/login  
   Użytkownik otrzymuje token JWT.
2. Rozpoczęcie gry: POST /api/games  
   Tworzy nową grę i sesję.
3. Pobranie aktualnego kroku: GET /api/steps/current  
   Zwraca pierwszy krok scenariusza.
4. Podejmowanie decyzji: POST /api/steps/decision  
   Przekazuje wybór i przechodzi do następnego kroku.

- Powtarzanie kroków 3–4:  
  Powtarzaj do momentu zakończenia gry lub zakończenia sesji.

# TODO

- specify which endpoint require JWT token
- specify return status codes
- specify payloads for endpoints
- specify returned data

# Do rozważenia

1. Przy DELETE zasobu, według mnie w przypadku nie znalezienia, powinniśmy zwracać 200, ponieważ w ostateczności osiągneliśmy cel i zasobu nie ma na serwerze.
2. Jak określić błędy i wiadomości czy zakodować to jako

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

3. Czy w JWT kodowac id użytkownika czy id sesji, jak podtrzymać sesje na mobilce, dekodować token czy po wyjsciu zapisywać id do local storage

# Uwagi

- **PUT** jest odpowienikiem **GET**, więc przy aktualizacji, powinien on wstawiać całkowicie świeży zasób, nie aktualizaowac jego część.
