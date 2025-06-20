# 🌦️ ClimaTrack  
**A Weather Analytics and Climate Trend Detection System**

---

## 📌 Objective

To collect, store, and analyze weather data (temperature, humidity, wind speed, and conditions) over time for multiple Indian cities using the **OpenWeatherMap API**.

We will use:
- **PostgreSQL** for structured storage
- **Python** for automation, data analysis, and visualization
- **Excel** for business-ready dashboarding with formula-driven KPIs

---

## 🧰 Tech Stack

| Component          | Technology                                          |
|--------------------|-----------------------------------------------------|
| Data Source         | OpenWeatherMap API                                  |
| ETL Script          | Python (`requests`, `pandas`, `sqlalchemy`)         |
| Database            | PostgreSQL                                          |
| Analytics           | SQL queries                                         |
| Visualization       | Python (`matplotlib`, `seaborn`, `plotly`)          |
| Business Dashboard  | Excel with pivot tables, slicers, KPIs              |

---

## 🏙️ Cities Covered

- Mumbai  
- Delhi  
- Chennai  
- Kolkata  
- Bangalore  
- Hyderabad  

---

## 📦 Phase 1: PostgreSQL Database Schema

File: `weather_data.sql`

```sql
CREATE TABLE weather_data (
    id SERIAL PRIMARY KEY,
    city TEXT,
    temperature NUMERIC,
    humidity NUMERIC,
    wind_speed NUMERIC,
    weather_description TEXT,
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
````

---

## ⚙️ Phase 2: Python Script (Data Fetching + Storage)

File: `scripts/fetch_and_store.py`

```python
import requests
import pandas as pd
from sqlalchemy import create_engine
from datetime import datetime

API_KEY = 'your_api_key_here'
CITIES = ['Mumbai', 'Delhi', 'Chennai', 'Kolkata', 'Bangalore', 'Hyderabad']
BASE_URL = 'http://api.openweathermap.org/data/2.5/weather'

def get_weather(city):
    res = requests.get(BASE_URL, params={
        'q': city,
        'appid': API_KEY,
        'units': 'metric'
    }).json()

    return {
        'city': city,
        'temperature': res['main']['temp'],
        'humidity': res['main']['humidity'],
        'wind_speed': res['wind']['speed'],
        'weather_description': res['weather'][0]['description'],
        'timestamp': datetime.now()
    }

# Collect and store data
data = [get_weather(city) for city in CITIES]
df = pd.DataFrame(data)
engine = create_engine('postgresql://username:password@localhost/climatrack_db')
df.to_sql('weather_data', engine, if_exists='append', index=False)
```

> 💡 **Note:** Run `fetch_and_store.py` every 1 hour (or 30 mins) to collect time-series weather data across cities.

---

## 🗃️ Phase 3: Sample Output in pgAdmin

Run this query in pgAdmin:

```sql
SELECT * FROM weather_data ORDER BY timestamp DESC;
```

You will see:

| id | city   | temperature | humidity | wind\_speed | weather\_description | timestamp           |
| -- | ------ | ----------- | -------- | ----------- | -------------------- | ------------------- |
| 1  | Mumbai | 30.5        | 72       | 2.3         | scattered clouds     | 2025-06-20 13:00:00 |
| 2  | Delhi  | 38.1        | 42       | 4.8         | clear sky            | 2025-06-20 13:00:00 |
| .. | ...    | ...         | ...      | ...         | ...                  | ...                 |

---

## 🧠 Phase 4: SQL Analysis Queries

```sql
-- 🔍 Temperature Spike Analysis
SELECT city,
       MAX(temperature) AS max_temp,
       MIN(temperature) AS min_temp,
       MAX(temperature) - MIN(temperature) AS temp_spike
FROM weather_data
GROUP BY city
ORDER BY temp_spike DESC;
```
Original Table:

![image](https://github.com/user-attachments/assets/64adeb1a-7e79-4125-8256-83d9065d062e)

Output of above query:

![image](https://github.com/user-attachments/assets/20869539-bde0-4baa-81f0-141da111d8ca)

> Use this to detect which city shows the **greatest variation in temperature** over time.

More SQL queries can include:

* Average temperature by hour or day
* Humidity trends
* Cities with consistently high wind speeds

---

## 📊 Phase 5: Excel Dashboard

* Import data from PostgreSQL or CSV exported from pgAdmin
* Create a Pivot Table with slicers for:

  * City
  * Date
* Add KPIs:

  * Max/Min/Avg Temperature
  * Temp Spike
  * Last 24-hour Change
* Include charts like:

  * Line chart (Temp over Time)
  * Bar chart (City-wise Comparison)

---

## 📈 Phase 6: Python Visualizations

Use tools like:

* `matplotlib` or `seaborn` for trend lines
* `plotly` for interactive dashboards

Example:

```python
import seaborn as sns
import matplotlib.pyplot as plt
import pandas as pd
from sqlalchemy import create_engine

# DB connection
engine = create_engine('postgresql+psycopg2://postgres:password@localhost/climatrack_db')

# Load data
df = pd.read_sql("SELECT * FROM weather_data", engine)

# Plot
sns.lineplot(data=df, x='timestamp', y='temperature', hue='city')
plt.title("Temperature Trends Over Time")
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()

```

![image](https://github.com/user-attachments/assets/b4294bfc-1555-4f60-8f4d-71ed2bb0dd5b)

---

## 📅 Future Scope

* Integrate rainfall or UV index
* Add historical weather backfill
* Build a Flask dashboard or use Power BI
* Forecasting models using `Prophet` or `ARIMA`

---

## 🧠 Insight Use Cases

* Detect climate changes over time
* Compare city-level weather volatility
* Track rising temperature patterns
* Build local warning systems for extreme weather

---

> ✅ A complete **capstone-worthy** project combining real-world data engineering, SQL analysis, Excel dashboards, and Python automation!

---

## 🧑‍💻 Author

**Ashu Davale**
Weather Analyst | Data Enthusiast







