# WeatherApp2025
def get_weather(city):
    url = f"https://geocoding-api.open-meteo.com/v1/search?name={city}&count=1"
    response = requests.get(url)
    data = response.json()

    if "results" not in data or len(data["results"]) == 0:
        return "City not found."

    latitude = data["results"][0]["latitude"]
    longitude = data["results"][0]["longitude"]

    weather_url = (
        f"https://api.open-meteo.com/v1/forecast?latitude={latitude}"
        f"&longitude={longitude}&current_weather=true"
    )
    weather_response = requests.get(weather_url)
    weather_data = weather_response.json()

    temperature = weather_data["current_weather"]["temperature"]
    windspeed = weather_data["current_weather"]["windspeed"]

    return f"Current temperature in {city}: {temperature}°C, Wind Speed: {windspeed} km/h"

# User input
city_name = input("Enter a city name: ")
print(get_weather(city_name))


