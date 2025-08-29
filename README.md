# Zifala Full Stack Challenge – Country Distances App

## Overview

This application computes all **unique country pairs** sorted by the shortest distance between their capitals, based on a dataset of countries with latitude and longitude.

* **Backend**: Laravel 10
* **Frontend**: Blade + JavaScript (Axios for API calls)
* **Live updates**: SSE (Server-Sent Events)
* **Data source**: `storage/app/data/countries.json`

---

## 1️⃣ Backend / API

### Dataset

* Stored in `countries.json` (ISO2, ISO3, name, capital, lat, lon)
  Example record:

```json
{
  "iso2": "SO",
  "iso3": "SOM",
  "name": "Somalia",
  "capital": "Mogadishu",
  "lat": 2.0469,
  "lon": 45.3182
}
```

### Routes / Endpoints

#### **Admin UI / CRUD**

* `GET /country-distances` – Shows the admin page with existing distance records
* `POST /country-distances/create` – Create a new record, validate ISO codes, compute/cached pairs
* `POST /country-distances/getSingle` – Get a single record by ID
* `POST /country-distances/delete` – Delete a record
* `GET /country-distances/getPairs/{id}` – Get computed pairs for a record (JSON)
* `GET /country-distances/top20` – Returns top 20 shortest pairs across all dataset
* `POST /country-distances/export` – Export selected countries as Excel
* `POST /country-distances/import` – Import countries from Excel/CSV (**also updates map view**)
* `GET /country-distances/sample` – Download a sample template Excel
* `POST /country-distances/exportCsv` – Export all computed pairs as CSV
* `POST /country-distances/exportTop20` – Export top 20 pairs as CSV
* `GET /country-distances/stream/{id}` – SSE stream of a record’s computed pairs

#### **Challenge API**

* `GET /api/countries` – Returns full dataset
* `POST /api/distances`

  * **Body**: `{"countries":["SO","KE","ET","DJ"]}`
  * Validates codes, computes unique pairs, caches results for repeated requests
  * Returns JSON:

```json
{
  "pairs": [
    {"a":"DJ","b":"SO","km":1165.4},
    {"a":"KE","b":"SO","km":1374.9}
  ],
  "count": 6,
  "unit": "km",
  "id": 1
}
```

* SSE-compatible: `/api/distances/{id}/stream` streams each pair progressively.

---

## 2️⃣ Key Features

### Pair Computation

* **Haversine formula** for great-circle distance
* Generates **all unique pairs** (n → n\*(n-1)/2)
* Sorted ascending by kilometers
* Cached by **set-membership hash** (`SHA256` of sorted ISO codes) for repeated queries

### Admin Blade UI

* Displays list of distance records
* Buttons for: Update, Delete, Show Map (top 20 shortest pairs)
* Status badges: `Pending`, `Processing`, `Completed`

### Excel & CSV Support

* Export selected countries (`.xlsx`)
* Export all pairs (`.csv`)
* Export top 20 shortest pairs (`.csv`)
* Sample template for importing countries
* **CSV upload** also triggers map view display for uploaded countries

### Import Countries

* Validates uploaded Excel/CSV
* Skips duplicates
* Auto-generates `CountryDistance` records on successful import
* Rollback if fewer than 2 valid countries

### Live Streaming (SSE)

* Streams pair-by-pair computation for frontend progress bar
* Includes `a`, `b`, `km`, `a_lat`, `a_lon`, `b_lat`, `b_lon`
* Usable for admin UI or API clients

---

## 3️⃣ Frontend Features

* Select countries and submit request
* Live progress bar fed by SSE
* Table of results, sortable by distance
* Export table as CSV or Excel
* Map view for top 20 shortest pairs (**also for CSV uploads**)

---

## 4️⃣ Performance & Complexity

* Pair computation: **O(n²)**
* Sorting pairs: **O(n² log n²)** ≈ **O(n² log n)**
* SSE streams results progressively to avoid UI blocking
* Caching repeated requests improves performance significantly

---

## 5️⃣ Running Locally

```bash
git clone <repo>
composer install
cp .env.example .env
php artisan key:generate
php artisan migrate
php artisan serve
```

Frontend: access via Blade views (`/country-distances`)

---

## 6️⃣ Running Tests

```bash
vendor/bin/phpunit
```

* Tests for Haversine function, API validation, sorting correctness, and integration tests for small datasets

---

## 7️⃣ AI Usage

* Generated initial boilerplate code for API endpoints
* Assisted in pair computation logic
* Helped with documentation and SSE implementation

---

## 8️⃣ Bonus Features Implemented

* Top 20 shortest pairs map view
* CSV export of all pairs and top 20
* Caching repeated requests by set-membership hash
* Excel import/export
* SSE live streaming
* Map view also updates on CSV upload

---

## 9️⃣ Time Complexity Analysis

| Operation                     | Complexity                 |
| ----------------------------- | -------------------------- |
| Pair generation (n countries) | O(n²)                      |
| Sorting pairs                 | O(n² log n²) ≈ O(n² log n) |
| SSE streaming                 | O(n²)                      |
| Caching lookup                | O(1)                       |

---

## 10️⃣ Deployment

* Public URL: [https://zifalla.ilaysschool.com/dashboard](https://zifalla.ilaysschool.com/dashboard)
* Username: `ziffala@gmail.com`
* Password: `ziffala@1234`
* SSE and map features are fully functional on this deployment



