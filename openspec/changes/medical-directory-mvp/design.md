# Design: Medical Directory MVP

## Overview

A full-stack web application providing English-language hospital rankings by medical specialty in China. The system presents pre-seeded ranking data (based on Fudan Hospital Rankings / 复旦版医院排行榜) through a RESTful API consumed by a React SPA.

## Architecture

```
┌─────────────────────────────────────────────────┐
│                   Frontend                       │
│         React 19 + Vite + TypeScript            │
│                                                 │
│  ┌───────────┐  ┌──────────┐  ┌─────────────┐  │
│  │ Home Page │  │ Specialty │  │  Hospital   │  │
│  │(specialty │  │  Ranking  │  │  Profile    │  │
│  │  list)    │  │  Page     │  │  Page       │  │
│  └───────────┘  └──────────┘  └─────────────┘  │
│                                                 │
│         React Router + Fetch API                │
└─────────────────┬───────────────────────────────┘
                  │ /api/*
┌─────────────────▼───────────────────────────────┐
│                   Backend                        │
│        Spring Boot 3.4 + Java 21                │
│                                                 │
│  ┌────────────────────────────────────────────┐ │
│  │  REST Controllers                          │ │
│  │  GET /api/specialties                      │ │
│  │  GET /api/specialties/{id}/rankings        │ │
│  │  GET /api/hospitals                        │ │
│  │  GET /api/hospitals/{id}                   │ │
│  │  GET /api/hospitals/search?q=&city=&spec=  │ │
│  └────────────────────────────────────────────┘ │
│  ┌────────────┐  ┌────────────┐  ┌───────────┐ │
│  │  Service   │  │ Repository │  │   Model   │ │
│  │  Layer     │  │  (JPA)     │  │  Entities │ │
│  └────────────┘  └────────────┘  └───────────┘ │
│                                                 │
│              H2 (dev) / PostgreSQL (prod)        │
└─────────────────────────────────────────────────┘
```

## Data Model

### Entity: Specialty (科室分类)
| Field        | Type    | Description                    |
|--------------|---------|--------------------------------|
| id           | Long    | Primary key                    |
| name         | String  | English name (e.g., "Orthopedics") |
| name_cn      | String  | Chinese name (e.g., "骨科")     |
| description  | String  | Brief English description      |
| icon         | String  | Icon identifier / emoji        |

### Entity: Hospital (医院)
| Field               | Type    | Description                          |
|---------------------|---------|--------------------------------------|
| id                  | Long    | Primary key                          |
| name                | String  | English name                         |
| name_cn             | String  | Chinese name                         |
| city                | String  | City in English                      |
| province            | String  | Province in English                  |
| address             | String  | Address in English                   |
| phone               | String  | Contact phone                        |
| website             | String  | Hospital website URL                 |
| description         | String  | English description                  |
| has_international   | Boolean | Has international/VIP department     |
| image_url           | String  | Hospital image                       |

### Entity: SpecialtyRanking (科室排名)
| Field         | Type    | Description                         |
|---------------|---------|-------------------------------------|
| id            | Long    | Primary key                         |
| specialty_id  | Long    | FK → Specialty                      |
| hospital_id   | Long    | FK → Hospital                       |
| rank_position | Integer | Rank (1, 2, 3, ...)                 |
| tier          | String  | Tier level (e.g., "Top 10", "Top 20") |
| year          | Integer | Ranking year (e.g., 2023)           |
| source        | String  | "Fudan Hospital Rankings"           |

## API Design

### GET /api/specialties
List all specialties.
```json
[
  { "id": 1, "name": "Orthopedics", "nameCn": "骨科", "description": "...", "icon": "🦴" }
]
```

### GET /api/specialties/{id}/rankings?city={city}&year={year}
Get hospital rankings for a specialty, optionally filtered by city/year.
```json
{
  "specialty": { "id": 1, "name": "Orthopedics" },
  "year": 2023,
  "rankings": [
    {
      "rankPosition": 1,
      "tier": "Top 10",
      "hospital": { "id": 101, "name": "Peking University Third Hospital", "city": "Beijing", "hasInternational": true }
    }
  ]
}
```

### GET /api/hospitals?q={query}&city={city}&specialty={specialtyId}&page={page}
Search/filter hospitals with pagination.
```json
{
  "content": [...],
  "totalElements": 120,
  "totalPages": 12,
  "page": 0
}
```

### GET /api/hospitals/{id}
Get hospital detail.
```json
{
  "id": 101,
  "name": "Peking University Third Hospital",
  "nameCn": "北京大学第三医院",
  "city": "Beijing",
  "address": "49 North Garden Road, Haidian District, Beijing",
  "phone": "+86-10-82266699",
  "description": "...",
  "hasInternational": true,
  "topSpecialties": [
    { "specialtyName": "Orthopedics", "rankPosition": 1 }
  ]
}
```

## Frontend Pages

| Route                        | Page              | Description                              |
|------------------------------|-------------------|------------------------------------------|
| `/`                          | Home              | Specialty category grid + search bar     |
| `/specialties/:id`           | Specialty Ranking | Ranked hospital list for a specialty     |
| `/hospitals/:id`             | Hospital Profile  | Detailed hospital information            |
| `/search?q=&city=&specialty=`| Search Results    | Hospital search results with filters     |

## Mock Data Scope
- **10+ specialties**: Orthopedics, Cardiology, Oncology, Neurology, Ophthalmology, Gastroenterology, Respiratory, Urology, Endocrinology, General Surgery
- **50+ hospitals**: Major tertiary hospitals across Beijing, Shanghai, Guangzhou, Chengdu, Hangzhou, etc.
- **Rankings**: Top 10 hospitals per specialty (based on 复旦版排行榜 2023)

## Dependencies
- Backend: Spring Boot Starter Web, Spring Data JPA, H2, Flyway (migrations)
- Frontend: React Router, (CSS framework TBD — likely Tailwind CSS)

## Implementation Strategy
1. Backend: Define entities → Create seed data (data.sql) → Build REST APIs → Add search/filter
2. Frontend: Set up routing → Build pages (Home → Ranking → Hospital) → Connect API → Style
3. Integration: Verify full flow end-to-end
