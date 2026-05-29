from IPython.display import HTML
import requests
import pandas as pd

LAT = 49.4333
LON = 2.0833

url = "https://api.open-meteo.com/v1/forecast"

params = {
    "latitude": LAT,
    "longitude": LON,
    "hourly": [
        "temperature_2m",
        "precipitation",
        "rain",
        "wind_speed_10m"
    ],
    "forecast_days": 4,
    "timezone": "Europe/Paris"
}

r = requests.get(url, params=params)

data = r.json()

df = pd.DataFrame({
    "Heure_Raw": data["hourly"]["time"],
    "Pluie (mm)": data["hourly"]["rain"],
    "Précipitations (mm)": data["hourly"]["precipitation"],
})
df['Heure_Raw'] = pd.to_datetime(df['Heure_Raw'])

df['Date'] = df['Heure_Raw'].dt.strftime('%d/%m/%Y')
df['Heure'] = df['Heure_Raw'].dt.strftime('%H:%M')

df = df[['Date', 'Heure', 'Pluie (mm)', 'Précipitations (mm)']]

html_table = df.to_html(index=False, escape=False)

html_output = f"""
<html lang="fr">
<head>
<meta charset="utf-8">
<title>Prévisions météo Beauvais</title>
<style>
  body{{font-family:system-ui,-apple-system,Segoe UI,Roboto,Arial;margin:20px}}
  table{{border-collapse:collapse;width:100%;max-width:800px; margin-top: 15px;}}
  th,td{{border:1px solid #ddd;padding:8px;text-align:right}}
  th{{background:#f3f3f3;text-align:left}}
  caption{{font-weight:600;margin-bottom:8px;text-align:left}}
</style>
</head>
<body>
{html_table}
</body>
</html>
"""
display(HTML(html_output))
