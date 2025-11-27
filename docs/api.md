# Tanulási Platform API Dokumentáció

## Áttekintés

**Alap URL:** `{{https://127.0.0.1/learningPlatformBearer/public/api}}`

---

## Hitelesítés

Az API **Bearer Token hitelesítést** használ a védett végpontokhoz. Sikeres bejelentkezés után a tokent az Authorization fejlécben kell elküldeni:

```
Authorization: Bearer {token_értéke}
```

---

## Végpontok

### Regisztráció

Új felhasználói fiók létrehozása.

**Végpont:** `POST /register`

**Hitelesítés:** Nem szükséges

**Fejlécek:**
```
Accept: application/json
Content-Type: application/json
```

**Kérés törzse:**
```json
{
    "name": "kunta",
    "email": "kunta@example.hu",
    "password": "Jelszo_2025",
    "password_confirmation": "Jelszo_2025"
}
```

**Paraméterek:**

| Mező | Típus | Kötelező | Leírás |
|------|-------|----------|--------|
| name | string | Igen | Felhasználó teljes neve |
| email | string | Igen | Érvényes email cím |
| password | string | Igen | Jelszó (minimum követelmények vonatkoznak) |
| password_confirmation | string | Igen | Meg kell egyeznie a jelszó mezővel |

**Sikeres válasz:**

- **Kód:** 200 OK
- **Tartalom:**
```json
{
    "message": "Felhasználó sikeresen regisztrálva",
    "user": {
        "id": 1,
        "name": "kunta",
        "email": "kunta@example.hu"
    },
    "token": "1|aigX1in5x7oXUY0ynOUnkhti6hHxbHVQFaQW9W1df000c122"
}
```

**Hibaválasz:**

- **Kód:** 422 Unprocessable Entity
- **Tartalom:**
```json
{
    "message": "Validációs hiba",
    "errors": {
        "email": ["Ez az email cím már használatban van."]
    }
}
```

---

### Bejelentkezés

Felhasználó hitelesítése és bearer token visszaadása.

**Végpont:** `POST /login`

**Hitelesítés:** Nem szükséges

**Fejlécek:**
```
Accept: application/json
Content-Type: application/json
```

**Kérés törzse:**
```json
{
    "email": "kunta@example.hu",
    "password": "Jelszo_2025"
}
```

**Paraméterek:**

| Mező | Típus | Kötelező | Leírás |
|------|-------|----------|--------|
| email | string | Igen | Felhasználó email címe |
| password | string | Igen | Felhasználó jelszava |

**Sikeres válasz:**

- **Kód:** 200 OK / 201 Created
- **Tartalom:**
```json
{
    "message": "Sikeres bejelentkezés",
    "user": {
        "id": 1,
        "name": "kunta",
        "email": "kunta@example.hu"
    },
    "token": "1|aigX1in5x7oXUY0ynOUnkhti6hHxbHVQFaQW9W1df000c122"
}
```

**Hibaválasz:**

- **Kód:** 401 Unauthorized
- **Tartalom:**
```json
{
    "message": "Érvénytelen bejelentkezési adatok"
}
```

---

### Kijelentkezés

Az aktuális hitelesítési token visszavonása.

**Végpont:** `POST /logout`

**Hitelesítés:** Szükséges (Bearer Token)

**Fejlécek:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer {token}
```

**Kérés törzse:**
```json
{
    "email": "kunta@example.hu",
    "password": "Jelszo_2025"
}
```

**Sikeres válasz:**

- **Kód:** 200 OK / 201 Created / 204 No Content
- **Tartalom:**
```json
{
    "message": "Sikeres kijelentkezés"
}
```

**Hibaválasz:**

- **Kód:** 401 Unauthorized
- **Tartalom:**
```json
{
    "message": "Hitelesítés szükséges"
}
```

---

### Beiratkozás Kurzusra

A hitelesített felhasználó beiratkozása egy meghatározott kurzusra.

**Végpont:** `POST /courses/{course_id}/enroll`

**Hitelesítés:** Szükséges (Bearer Token)

**Fejlécek:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer {token}
```

**URL paraméterek:**

| Paraméter | Típus | Kötelező | Leírás |
|-----------|-------|----------|--------|
| course_id | integer | Igen | A kurzus azonosítója, amire beiratkozik |

**Kérés törzse:** Üres

**Példa kérés:**
```
POST /courses/1/enroll
```

**Sikeres válasz:**

- **Kód:** 200 OK / 202 Accepted / 204 No Content
- **Tartalom:**
```json
{
    "message": "Sikeres beiratkozás a kurzusra",
    "enrollment": {
        "id": 1,
        "user_id": 1,
        "course_id": 1,
        "enrolled_at": "2025-11-27T10:30:00.000000Z"
    }
}
```

**Hibaválasz:**

- **Kód:** 404 Not Found
- **Tartalom:**
```json
{
    "message": "A kurzus nem található"
}
```

- **Kód:** 409 Conflict
- **Tartalom:**
```json
{
    "message": "Már beiratkozott erre a kurzusra"
}
```

---

### Kurzus Befejezettnek Jelölése

Kurzus befejezettnek jelölése a hitelesített felhasználó számára.

**Végpont:** `PATCH /courses/{course_id}/completed`

**Hitelesítés:** Szükséges (Bearer Token)

**Fejlécek:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer {token}
```

**URL paraméterek:**

| Paraméter | Típus | Kötelező | Leírás |
|-----------|-------|----------|--------|
| course_id | integer | Igen | A befejezettnek jelölendő kurzus azonosítója |

**Kérés törzse:** Üres

**Példa kérés:**
```
PATCH /courses/1/completed
```

**Sikeres válasz:**

- **Kód:** 200 OK
- **Tartalom:**
```json
{
    "message": "Kurzus befejezettnek jelölve",
    "enrollment": {
        "id": 1,
        "user_id": 1,
        "course_id": 1,
        "completed_at": "2025-11-27T12:45:00.000000Z"
    }
}
```

**Hibaválasz:**

- **Kód:** 404 Not Found
- **Tartalom:**
```json
{
    "message": "Beiratkozás nem található"
}
```

- **Kód:** 400 Bad Request
- **Tartalom:**
```json
{
    "message": "A kurzus már be van jelölve befejezettnek"
}
```

---

## Válaszkódok

| Kód | Leírás |
|-----|--------|
| 200 OK | Sikeres kérés |
| 201 Created | Erőforrás sikeresen létrehozva |
| 202 Accepted | Kérés elfogadva feldolgozásra |
| 204 No Content | Sikeres kérés, nincs visszaadandó tartalom |
| 400 Bad Request | Érvénytelen kérés formátum vagy paraméterek |
| 401 Unauthorized | Hitelesítés szükséges vagy érvénytelen token |
| 404 Not Found | A keresett erőforrás nem található |
| 409 Conflict | A kérés ütközik a jelenlegi állapottal |
| 422 Unprocessable Entity | Validációs hiba |
| 500 Internal Server Error | Szerverhiba |

---

## Hibakezelés

Minden hibaválasz a következő formátumot követi:

```json
{
    "message": "Hiba leírása",
    "errors": {
        "mező_neve": [
            "Hiba részletei ehhez a mezőhöz"
        ]
    }
}
```

---

## Használati Példák

### Teljes Hitelesítési Folyamat

1. **Új felhasználó regisztrálása:**
```bash
curl -X POST {{base_url}}/register \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Teszt Elek",
    "email": "teszt@example.hu",
    "password": "BiztonságosJelszó123",
    "password_confirmation": "BiztonságosJelszó123"
  }'
```

2. **Bejelentkezés:**
```bash
curl -X POST {{base_url}}/login \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "teszt@example.hu",
    "password": "BiztonságosJelszó123"
  }'
```

3. **Beiratkozás kurzusra (bejelentkezéskor kapott token használatával):**
```bash
curl -X POST {{base_url}}/courses/1/enroll \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer 1|aigX1in5x7oXUY0ynOUnkhti6hHxbHVQFaQW9W1df000c122"
```

4. **Kurzus befejezettnek jelölése:**
```bash
curl -X PATCH {{base_url}}/courses/1/completed \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer 1|aigX1in5x7oXUY0ynOUnkhti6hHxbHVQFaQW9W1df000c122"
```

5. **Kijelentkezés:**
```bash
curl -X POST {{base_url}}/logout \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer 1|aigX1in5x7oXUY0ynOUnkhti6hHxbHVQFaQW9W1df000c122"
```

---

## Megjegyzések

- Minden végpont JSON adatokat fogad és ad vissza
- Mindig add meg az `Accept: application/json` fejlécet
- A tokeneket a Laravel Sanctum generálja
- A tokeneket biztonságosan kell tárolni a kliens oldalon
- Érvénytelen vagy lejárt tokenek 401 Unauthorized választ eredményeznek

---

**Utolsó frissítés:** 2025. november 27.
