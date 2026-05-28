import requests
import pandas as pd

# Coordonnées Beauvais
LAT = 49.4333
LON = 2.0833

url = "https://api.open-meteo.com/v1/forecast"

params = {
    "latitude": LAT,
    "longitude": LON,
    "hourly": [

        "precipitation",
        "rain",
    ],
    "forecast_days": 4,
    "timezone": "Europe/Paris"
}

# Requête API
r = requests.get(url, params=params)

# Conversion JSON
data = r.json()

# Création du tableau
df = pd.DataFrame({
    "Heure_Raw": data["hourly"]["time"],
    "Pluie (mm)": data["hourly"]["rain"],
    "Précipitations (mm)": data["hourly"]["precipitation"],
})

# Convert 'Heure_Raw' to datetime objects
df['Heure_Raw'] = pd.to_datetime(df['Heure_Raw'])

# Create new 'Date' and 'Heure' columns
df['Date'] = df['Heure_Raw'].dt.strftime('%d/%m/%Y') # Modified format here
df['Heure'] = df['Heure_Raw'].dt.strftime('%H:%M')

# Drop the raw 'Heure_Raw' column and reorder columns for a cleaner display
df = df[['Date', 'Heure', 'Pluie (mm)', 'Précipitations (mm)']]

# Affichage
print("\nPrévisions météo Beauvais")
display(df)
