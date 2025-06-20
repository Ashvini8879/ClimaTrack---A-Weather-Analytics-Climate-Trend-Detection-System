# ClimaTrack:-       A Weather Analytics and Climate Trend Detection System



# Objective:
To collect, store, and analyze weather data (temperature, humidity, wind speed, and conditions) over time for multiple Indian cities using OpenWeatherMap API. We'll use PostgreSQL for structured storage, Python for automation + data analysis + visualization, and Excel for business-ready dashboarding with formula-driven KPIs.




# Tech Stack:

| Component              | Technology                                          |
| ---------------------- | --------------------------------------------------- |
| Data Source            | OpenWeatherMap API                                  |
| ETL Script             | Python (`requests`, `pandas`, `sqlalchemy`)         |
| Database               | PostgreSQL                                          |
| Analytics              | SQL queries                                         |
| Visualization          | Python (`matplotlib`, `seaborn`, `plotly`)          |
| Business Dashboard     | Excel with advanced formulas, pivot tables, slicers |




# Cities Covered:
Mumbai
Delhi
Chennai
Kolkata
Bangalore
Hyderabad

        

# Phase 1: PostgreSQL Database Schema

-- weather_data.sql
CREATE TABLE weather_data (
    id SERIAL PRIMARY KEY,
    city TEXT,
    temperature NUMERIC,
    humidity NUMERIC,
    wind_speed NUMERIC,
    weather_description TEXT,
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);



# Phase 2: Python Script (Data Fetching + Storage)


file name : scripts/fetch_and_store.py
import requests, pandas as pd
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

return{
        'city': city,
        'temperature': res['main']['temp'],
        'humidity': res['main']['humidity'],
        'wind_speed': res['wind']['speed'],
        'weather_description': res['weather'][0]['description'],
        'timestamp': datetime.now()
    }

data = [get_weather(city) for city in CITIES]
df = pd.DataFrame(data)
engine = create_engine('postgresql://username:password@localhost/climatrack_db')  #postgres conn
df.to_sql('weather_data', engine, if_exists='append', index=False)

# NOTE : run fetch_and_store.py script every 1 hour (or even every 30 minutes) so it stores fresh data in your weather_data table with a new timestamp for analysis.

# OUTPUT in pgAdmin:- 


select * from weather_data;

![image](https://github.com/user-attachments/assets/2dd957c5-1a15-4d54-b487-12da0b79d699)


# Phase 3: SQL Analysis Queries 

