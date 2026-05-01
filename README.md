# PishyFal API Documentation

**Base URL:** `http://localhost:4000/api/v1`  
**Content-Type:** `application/json`

---

## Authentication

All protected routes require a Bearer token in the `Authorization` header:

```
Authorization: Bearer <token>
```

---

## Health

### GET `/health`

Check server status.

**Response**
```json
{ "status": "ok" }
```

---

## Auth

### POST `/auth/send-otp`

Send OTP to a phone number. Creates the user if they don't exist.

**Request Body**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| phone | string | Yes | Iranian phone number (e.g. `09123456789`) |

**Response `200`**
```json
{ "message": "OTP sent" }
```

**Errors**

| Status | Description |
|--------|-------------|
| 400 | Invalid phone format |

---

### POST `/auth/verify-otp`

Verify OTP and receive a JWT token.

**Request Body**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| phone | string | Yes | Iranian phone number |
| otp | string | Yes | 6-digit OTP code |

**Response `200`**
```json
{
  "token": "eyJ...",
  "user": {
    "id": "...",
    "phone": "09123456789",
    "name": "",
    "coins": 6,
    "streak": 0,
    "lastVisitDate": null,
    "goldSkin": false,
    "hasTakenSurvey": false,
    "lotteryTickets": 0,
    "surveyAnswers": null,
    "age": null,
    "color": null
  }
}
```

**Errors**

| Status | Description |
|--------|-------------|
| 400 | Invalid phone/OTP format |
| 401 | OTP is incorrect or expired |

---

## User

### GET `/user/profile`

Get the authenticated user's profile.

**Auth required:** Yes

**Response `200`**
```json
{
  "id": "...",
  "refCode": "ABC123",
  "phone": "09123456789",
  "name": "Ali",
  "coins": 12,
  "streak": 5,
  "lastVisitDate": "2026-05-01T00:00:00.000Z",
  "goldSkin": false,
  "hasTakenSurvey": false,
  "lotteryTickets": 0,
  "surveyAnswers": null,
  "age": 25,
  "color": "#FF5733"
}
```

---

### PUT `/user/profile`

Update the authenticated user's profile.

**Auth required:** Yes

**Request Body** (all fields optional)

| Field | Type | Constraints |
|-------|------|-------------|
| name | string | 3–50 characters |
| age | number | 10–120 |
| color | string | Valid hex color (e.g. `#FF5733`) |

**Response `200`**
```json
{
  "id": "...",
  "name": "Ali",
  "age": 25,
  "color": "#FF5733",
  "coins": 12,
  "streak": 5
}
```

**Errors**

| Status | Description |
|--------|-------------|
| 400 | Validation error |
| 401 | Unauthorized |

---

## Fortune

### POST `/fortune`

Generate a personalized AI fortune. Costs **5 coins**.

**Auth required:** Yes

**Request Body**

| Field | Type | Required | Values / Constraints |
|-------|------|----------|----------------------|
| mood | string | Yes | `happy`, `sad`, `stressed`, `adventurous`, `romantic` |
| concern | string | Yes | `love`, `career`, `health`, `money`, `family` |
| name | string | No | 1–50 characters |
| luckyNumber | number | No | 1–100 |

**Response `200`**
```json
{
  "fortune": "Your fortune text here...",
  "id": "fortune_object_id"
}
```

**Errors**

| Status | Description |
|--------|-------------|
| 400 | Validation error or insufficient coins |
| 401 | Unauthorized |

---

### GET `/fortune`

Get all fortunes for the authenticated user, most recent first.

**Auth required:** Yes

**Response `200`**
```json
{
  "count": 3,
  "data": [
    {
      "_id": "...",
      "userId": "...",
      "type": "personal",
      "fortuneText": "...",
      "mood": "happy",
      "concern": "career",
      "luckyNumber": 7,
      "coinsEarned": 0,
      "createdAt": "2026-05-01T10:00:00.000Z"
    }
  ]
}
```

---

### POST `/fortune/wheel`

Spin the fortune wheel. Maximum **2 spins per day** (resets at midnight). **3% chance** to win 6 coins.

**Auth required:** Yes

**Response `200`**
```json
{
  "isWinner": false,
  "coinsEarned": 0,
  "newCoins": 12,
  "remainingSpins": 1,
  "message": "Better luck next time"
}
```

**Response `200` (winner)**
```json
{
  "isWinner": true,
  "coinsEarned": 6,
  "newCoins": 18,
  "remainingSpins": 1,
  "message": "You won 6 coins!"
}
```

**Errors**

| Status | Description |
|--------|-------------|
| 400 | No spins remaining for today |
| 401 | Unauthorized |

---

## Leaderboard

### GET `/leaderboard`

Get the top 10 users ranked by streak.

**Auth required:** Yes

**Response `200`**
```json
[
  {
    "name": "Ali",
    "streak": 15,
    "isMe": true
  },
  {
    "name": "Sara",
    "streak": 12,
    "isMe": false
  }
]
```

---

## Error Responses

All errors follow this format:

```json
{ "message": "Error description" }
```

| Status | Meaning |
|--------|---------|
| 400 | Bad request / validation failed |
| 401 | Unauthorized (missing or invalid token) |
| 500 | Internal server error |

---

## Data Models

### User

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| phone | string | — | Unique Iranian phone number |
| name | string | `""` | Display name |
| coins | number | `6` | In-app currency |
| streak | number | `0` | Consecutive daily visit count |
| lastVisitDate | date | null | Last login date |
| goldSkin | boolean | `false` | Premium skin unlocked |
| hasTakenSurvey | boolean | `false` | Survey completion flag |
| lotteryTickets | number | `0` | Number of lottery tickets |
| surveyAnswers | object | null | Stored survey answers |
| refCode | string | — | Unique referral code |
| color | string | null | Hex color preference |
| age | number | null | User age |
| role | string | `"user"` | `user` or `admin` |

### Fortune

| Field | Type | Description |
|-------|------|-------------|
| userId | ObjectId | Reference to User |
| type | string | `personal` or `wheel` |
| fortuneText | string | The fortune content |
| mood | string | User mood at time of generation |
| concern | string | User's area of concern |
| luckyNumber | number | User's lucky number |
| coinsEarned | number | Coins earned from this fortune |
