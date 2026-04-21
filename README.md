# ⚽ Exploring African Talent in Europe — Data Management Project

> **Data Engineering & Management Project** | Master's in Data Science  
> Building a comprehensive scouting database of African footballers in Europe's top 5 leagues

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Selenium](https://img.shields.io/badge/Selenium-43B02A?style=for-the-badge&logo=selenium&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=for-the-badge&logo=pandas&logoColor=white)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)

---

## 📌 Project Overview

This project addresses the challenges football clubs face during player scouting and acquisition. By building a complete **data management pipeline** focused on African footballers competing in Europe's top five leagues, we created a unified and queryable player database to support data-driven recruitment decisions.

The pipeline covers the full data lifecycle: **collection → storage → profiling → integration → enrichment → analysis**.

**Goal:** Enable clubs to identify African talent that matches their tactical, physical, and financial requirements — reducing the time and cost of traditional scouting.

---

## 👥 Team & Roles

| Name | Role |
|------|------|
| **Stephen Adu Poku Yeboah** | SoFIFA web scraping (Selenium), data storage & SQL queries |
| Emmanuel Ampah | Data profiling & quality assessment, overall data analysis |
| Gianluca Licciardello | Transfermarkt scraping (BeautifulSoup), overall data analysis |

---

## 🗂️ Data Sources

| Source | Method | Data Collected |
|--------|--------|----------------|
| [SoFIFA](https://sofifa.com) (EA Sports) | Selenium (dynamic JS scraping) | 50+ player attributes: overall, potential, physical stats, skill ratings, market value, wage |
| [Transfermarkt](https://www.transfermarkt.co.uk) | BeautifulSoup (HTML parsing) | League, season, country, position, club, debut date, appearances, goals |

**Scope:** Top 15 African nations by FIFA ranking × Top 5 European leagues (EPL, La Liga, Serie A, Bundesliga, Ligue 1) × 3 seasons (2021/22, 2022/23, 2023/24)

---

## 🔧 Pipeline Architecture

```
┌─────────────────┐     ┌──────────────────┐
│  SoFIFA          │     │  Transfermarkt    │
│  (Selenium)      │     │  (BeautifulSoup)  │
└────────┬────────┘     └────────┬─────────┘
         │                       │
         ▼                       ▼
┌─────────────────────────────────────────┐
│         Data Storage (SQLite/MySQL)      │
│         table1 (SoFIFA) | table2 (TM)   │
└──────────────────┬──────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────┐
│     Data Profiling & Cleaning            │
│  • Missing values  • Type conversion     │
│  • Name normalization  • ID creation     │
└──────────────────┬──────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────┐
│     Data Integration (pd.merge on ID)   │
└──────────────────┬──────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────┐
│     Data Enrichment                      │
│  • FIFA-style player card scores         │
│  • Speed, Shooting, Passing, etc.        │
└──────────────────┬──────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────┐
│     Exploratory Analysis & SQL Queries   │
│  • Player distribution by league/country │
│  • FIFA ranking correlation (r up to 0.63)│
│  • Top players per season                │
└─────────────────────────────────────────┘
```

---

## 🔍 My Contribution — SoFIFA Scraping with Selenium & Data Storage

### Web Scraping (Selenium)
SoFIFA renders content dynamically via JavaScript, making standard HTTP scraping impossible. I used **Selenium WebDriver** to automate Chrome and extract data across multiple paginated pages for 3 seasons.

**Key implementation details:**
- Automated Chrome via `ChromeDriverManager` with custom timeout and logging settings
- Used `WebDriverWait` + `EC.presence_of_element_located` to handle dynamic content loading
- Implemented pagination by locating and clicking the **"Next"** button using JavaScript scrolling to avoid element visibility issues
- Extracted player rows using `find_elements(By.TAG_NAME, 'tr')` and saved to CSV via Pandas

**Post-scraping preprocessing:**
- Separated player name and role from a combined column
- Split team and contract into distinct columns
- Converted value/wage (string → integer), height (feet → cm), weight (lbs → kg)
- Standardized 25+ position abbreviations to unified role names (e.g., `LCB`, `RCB` → `Centre-Back`)
- Normalized player names (lowercase, removed accents) for cross-source matching
- Created a unique player ID: `first_letter_name + team + season`

### Data Storage (SQLite)
- Stored both scraped DataFrames into a **relational SQLite database** using SQLAlchemy (`df.to_sql()`)
- Structured data into two related tables visible in the DB Browser for SQLite interface

---

## 📊 Key Findings

### Player Distribution by League

| League | 2021/22 | 2022/23 | 2023/24 |
|--------|---------|---------|---------|
| Ligue 1 | 97 | 80 | **101** |
| Premier League | 39 | 39 | 47 |
| Serie A | 31 | 33 | 32 |
| La Liga | 16 | 26 | 25 |
| Bundesliga | 19 | 22 | 23 |

### Top Exporting Nations (2023/24)
Senegal (35), Ivory Coast (31), Morocco (31), Nigeria (19), Ghana (20)

### FIFA Ranking Correlation
Pearson correlation between European player count and national FIFA ranking points:

| Season | Pearson r |
|--------|-----------|
| 2021/22 | 0.45 |
| 2022/23 | 0.53 |
| 2023/24 | **0.63** |

→ Nations with more players in elite European leagues increasingly tend to perform better internationally.

### Top Players Across Seasons
Mohamed Salah (Egypt) and Achraf Hakimi (Morocco) maintained top-tier ratings across all three seasons. Ismael Bennacer's market value nearly doubled from €30M to €53.5M over the period.

---

## 📁 Repository Structure

```
african-talent-europe-data-management/
├── README.md
├── giocatori.ipynb              # Full pipeline: scraping, profiling, integration, enrichment, EDA
└── Data_Management_Report.pdf   # Full project report
```

---

## 🚀 How to Run

### Prerequisites
```bash
pip install selenium pandas beautifulsoup4 requests sqlalchemy webdriver-manager unidecode matplotlib seaborn
```

### Run the notebook
```bash
jupyter notebook giocatori.ipynb
```

> ⚠️ For the SoFIFA scraping cells: make sure Chrome is installed and `ChromeDriverManager` is up to date.  
> ⚠️ Transfermarkt scraping respects a `User-Agent` header — run responsibly and avoid hammering the server.

---

## 🛠️ Technologies

| Tool | Purpose |
|------|---------|
| Python | Core language |
| Selenium + ChromeDriver | Dynamic web scraping (SoFIFA) |
| BeautifulSoup + Requests | Static HTML scraping (Transfermarkt) |
| Pandas | Data manipulation & integration |
| SQLAlchemy + SQLite | Relational data storage |
| Matplotlib + Seaborn | Exploratory visualizations |
| SQL (GROUP BY queries) | Player distribution analysis |

---

## ⚠️ Known Limitations

- **ID mismatches** due to inconsistent player name formatting across sources (e.g., `"p. matar sarr"` vs `"p. sarr"`) — required manual fixes.
- Players who changed nationality mid-analysis (e.g., Rayan Aït Nouri, Ademola Lookman) caused completeness gaps.
- Players transferred mid-season had duplicate club entries — resolved manually.
- SoFIFA scraping is sensitive to website structure changes; selectors may need updating.

---

## 📄 License

Academic project. Data sourced from SoFIFA and Transfermarkt for educational purposes only.
