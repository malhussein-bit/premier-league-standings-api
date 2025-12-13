![Python](https://img.shields.io/badge/Python-3.12-blue)
![MySQL](https://img.shields.io/badge/MySQL-8.0-orange)
![Status](https://img.shields.io/badge/Status-Portfolio%20Ready-success)

# Premier League Standings ETL Pipeline SEASON 2021

### **API → Python → MySQL → Query-Ready Table**
### Overview
During live football matches in public venues, heavy traffic on shared networks can make accessing standings unreliable.
This project demonstrates how to retrieve live Premier League standings from an API, process them using Python, and persist them in a MySQL database for fast, reliable queryin

## Prerequisites
- Python 3.10+
- MySQL 8.0 running locally
- RapidAPI account with Premier League standings access

## **Tools Used**

| Component           | Technology                                   |
| ------------------- | -------------------------------------------- |
| **API**             | RapidAPI – Premier League Standings endpoint |
| **Data Processing** | Python (requests, json, pandas)              |
| **Database**        | MySQL 8.0                                    |
| **ETL Logic**       | Python + SQL (UPSERT)                        |
| **IDE**             | VS Code / Jupyter Notebook                   |
| **Version Control** | Git + GitHub                                 |

## **Project Objectives**

Connect to a live football standings API
Parse and transform raw JSON response into a clean tabular format
Implement SQL UPSERT to load/refresh standings
Build a reproducible ETL pipeline using Python
Store standings in a MySQL database for analytics



## Repository Structure


premier-league-standings-api/

 -  main.py                  # Python ETL script (API → MySQL)
 - analysis.ipynb           # Exploratory analysis & testing notebook
 - .env                     # API keys & DB secrets (ignored in Git)
 -  .gitignore               # Prevents secrets & temp files from committing
 -   README.md                # Project documentation




## **How the Pipeline Works**

### **1. Fetch Data from API (main.py)**

The Python script sends a GET request to the standings endpoint using your API key stored in `.env`.


```python
response = requests.get(API_URL, headers={"X-RapidAPI-Key": api_key})
data = response.json()
```



### **2. Transform JSON to Python Tuples**

The script extracts all required fields:

* season
* position
* team_id
* team name
* matches played, won, lost, draw
* goals for / against
* goal difference
* points
* form (WWDL etc.)

Results are stored in a list of Python tuples ready for SQL insertion.

## 3. SQL Table Schema

### **Table: standings**

```sql
CREATE TABLE standings (
    season INT NOT NULL,
    position INT NOT NULL,
    team_id INT NOT NULL,
    team VARCHAR(100),
    played INT,
    won INT,
    draw INT,
    lost INT,
    goals_for INT,
    goals_against INT,
    goal_diff INT,
    points INT,
    form VARCHAR(20),
    PRIMARY KEY (season, team_id)
);
```

### **4. Load to MySQL Using UPSERT**

To avoid duplicates, the SQL script uses:

```sql
INSERT INTO standings (
    season, position, team_id, team, played, won, draw, lost,
    goals_for, goals_against, goal_diff, points, form
)
VALUES (%s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s)
ON DUPLICATE KEY UPDATE
    position = VALUES(position),
    played = VALUES(played),
    won = VALUES(won),
    draw = VALUES(draw),
    lost = VALUES(lost),
    goals_for = VALUES(goals_for),
    goals_against = VALUES(goals_against),
    goal_diff = VALUES(goal_diff),
    points = VALUES(points),
    form = VALUES(form);
```


This ensures:

* No duplicated teams
* Standings update cleanly every run
* MySQL stays synced with the newest season



### **5. Confirmation of Load**

The notebook prints:


[SUCCESS] – Upsert attempted for 20 rows!
All database connections now closed.

### **6. Final MySQL Output**

The standings table is populated and validated in MySQL,
sorted by league position and ready for analytical queries.

The query below confirms that the standings table was successfully populated
and sorted correctly by league position.

Only the top teams are shown for clarity; the full dataset is available in MySQL
for further analysis and BI integration.


<img width="714" height="443" alt="Screenshot (635)" src="https://github.com/user-attachments/assets/aa1c44f3-6142-4fd5-a8b7-a39bee928cdb" />



---




## How to Run the Project

### **1. Clone the repository**

```bash
git clone https://github.com/malhussein-bit/premier-league-standings-api.git
```


### **2. Install dependencies**

```bash
pip install mysql-connector-python python-dotenv requests pandas
```

### **3. Update `.env` file**


API_KEY=rapid_api_key
MYSQL_USER=root
MYSQL_PASSWORD=xxxx
MYSQL_HOST=localhost
MYSQL_DATABASE=premier_league_db


### **4. Run the pipeline**

bash
python main.py




## Example SQL Queries for Analysis

**Teams sorted by points:**

sql
SELECT team, points 
FROM standings
ORDER BY points DESC;


**Best offensive teams (goals_for):**

sql
SELECT team, goals_for 
FROM standings 
ORDER BY goals_for DESC 
LIMIT 5;


**Win percentage:**

sql
*SELECT team,
       ROUND((won / played) * 100, 2) AS win_rate
*FROM standings
*ORDER BY win_rate DESC;




## Skills Demonstrated

* API integration
* ETL design
* Data cleansing
* Secure key management (.env + .gitignore)
* SQL schema & indexing
* MySQL UPSERT patterns
* Python error handling & transaction control
* Git version control
* Reproducible pipeline structure



## Contact

**Mohammad Al Hussein**
[alhussein.m65@gmail.com](mailto:alhussein.m65@gmail.com)
GitHub: [https://github.com/malhussein-bit](https://github.com/malhussein-bit)




