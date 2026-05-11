# 🎮 Free Fire Player API

A free, no-key-required REST API to fetch **Free Fire player information** and **ban status** — powered by internal Garena endpoints and protobuf decoding.

**Base URL:** `https://api2.nftoken.info`

---

## ✅ Features

- 🔍 Fetch full player profile (name, level, rank, guild, etc.)
- 🚫 Check if a player is banned and for how long
- 🌍 Auto-detects player region from UID
- 🔑 No API key required
- ⚡ Fast TTL-cached JWT tokens per region

---

## 📡 Endpoints

---

### 1. `GET /checkbanned`

Check if a player is banned + get basic player info.

**Request:**
```
GET https://api2.nftoken.info/checkbanned?id={UID}
```

| Parameter | Type   | Required | Description        |
|-----------|--------|----------|--------------------|
| `id`      | string | ✅ Yes   | Free Fire player UID |

**Example:**
```
GET https://api2.nftoken.info/checkbanned?id=123456789
```

**Response:**
```json
{
  "player_id": "123456789",
  "nickname": "PlayerName",
  "region": "IND",
  "level": 72,
  "is_banned": false,
  "ban_period": 0,
  "status": "NOT BANNED"
}
```

| Field        | Type    | Description                              |
|--------------|---------|------------------------------------------|
| `player_id`  | string  | The UID you queried                      |
| `nickname`   | string  | In-game name                             |
| `region`     | string  | Player's server region                   |
| `level`      | integer | Account level                            |
| `is_banned`  | boolean | `true` if currently banned               |
| `ban_period` | integer | Ban duration in days (0 if not banned)   |
| `status`     | string  | `"BANNED"` or `"NOT BANNED"`             |

---

### 2. `GET /player-info`

Fetch full detailed player profile. Region is auto-detected from UID.

**Request:**
```
GET https://api2.nftoken.info/player-info?uid={UID}
```

| Parameter | Type   | Required | Description          |
|-----------|--------|----------|----------------------|
| `uid`     | string | ✅ Yes   | Free Fire player UID |

**Example:**
```
GET https://api2.nftoken.info/player-info?uid=123456789
```

**Response:**
```json
{
  "AccountInfo": {
    "AccountAvatarId": 901000006,
    "AccountBPBadges": 120,
    "AccountBPID": 1014,
    "AccountBannerId": 901000037,
    "AccountCreateTime": 1592000000,
    "AccountEXP": 98000,
    "AccountLastLogin": 1706295767,
    "AccountLevel": 72,
    "AccountLikes": 4500,
    "AccountName": "PlayerName",
    "AccountRegion": "IND",
    "AccountSeasonId": 51,
    "AccountType": 1,
    "BrMaxRank": 220,
    "BrRankPoint": 3100,
    "CsMaxRank": 0,
    "CsRankPoint": 0,
    "EquippedWeapon": [],
    "ReleaseVersion": "OB51",
    "ShowBrRank": true,
    "ShowCsRank": false,
    "Title": 904000023
  },
  "AccountProfileInfo": {
    "EquippedOutfit": [211000050, 211000051],
    "EquippedSkills": [8, 3]
  },
  "GuildInfo": {
    "GuildCapacity": 50,
    "GuildID": "3001234567",
    "GuildLevel": 5,
    "GuildMember": 42,
    "GuildName": "EliteSquad",
    "GuildOwner": "987654321"
  },
  "captainBasicInfo": {},
  "creditScoreInfo": {},
  "petInfo": {},
  "socialinfo": {}
}
```

---

### 3. `GET /get`

Fetch full player profile with an optional manual region override. Useful if auto-detection is slow or incorrect.

**Request:**
```
GET https://api2.nftoken.info/get?uid={UID}&region={REGION}
```

| Parameter | Type   | Required | Description                            |
|-----------|--------|----------|----------------------------------------|
| `uid`     | string | ✅ Yes   | Free Fire player UID                   |
| `region`  | string | ❌ No    | Server region (see supported list below) |

**Example (auto region):**
```
GET https://api2.nftoken.info/get?uid=123456789
```

**Example (manual region):**
```
GET https://api2.nftoken.info/get?uid=123456789&region=IND
```

Response format is identical to `/player-info`.

---

### 4. `GET /check_key`

Check if the API requires a key.

**Request:**
```
GET https://api2.nftoken.info/check_key
```

**Response:**
```json
{
  "status": "no_key_required",
  "message": "This API does not require an API key for access"
}
```

---

### 5. `GET /refresh`

Force-refresh all internal JWT tokens for all regions. Useful if you're getting auth errors.

**Request:**
```
GET https://api2.nftoken.info/refresh
```

**Response:**
```json
{
  "message": "Tokens refreshed for all regions."
}
```

---

## 🌍 Supported Regions

| Code     | Region              |
|----------|---------------------|
| `IND`    | India               |
| `BD`     | Bangladesh          |
| `SG`     | Singapore           |
| `ID`     | Indonesia           |
| `TH`     | Thailand            |
| `VN`     | Vietnam             |
| `TW`     | Taiwan              |
| `BR`     | Brazil              |
| `US`     | United States       |
| `SAC`    | South America       |
| `NA`     | North America       |
| `RU`     | Russia              |
| `ME`     | Middle East         |
| `PK`     | Pakistan            |
| `CIS`    | CIS                 |
| `EUROPE` | Europe              |

---

## ⚠️ Error Responses

All errors return a JSON object with an `error` field.

| HTTP Code | Meaning                                      |
|-----------|----------------------------------------------|
| `400`     | Missing or invalid parameter                 |
| `500`     | Internal server error (bad UID, region fail) |

**Example:**
```json
{
  "error": "Player ID is required"
}
```

---

## 💡 Tips

- **Auto region detection** works by probing regions in priority order. It can take a few extra seconds for non-IND players — use `/get?uid=X&region=Y` for faster responses if you know the region.
- **Ban period** is in days. A value of `0` on a banned player means the ban is permanent or the duration wasn't returned by Garena.
- All responses are `Content-Type: application/json`.

---

## 📬 Contact

Made by **NF Token** — [nftoken.info](https://nftoken.info)
