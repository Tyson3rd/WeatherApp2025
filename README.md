# WeatherApp2025
def get_weather_data(city_name):
    """
    Retrieves current weather data for a given city using the Open-Meteo API.

    Args:
        city_name (str): The name of the city.

    Returns:
        dict or None: A dictionary containing the current weather information
                     or None if an error occurs.
    """
    geocoding_url = "https://geocoding-api.open-meteo.com/v1/search"
    forecast_url = "https://api.open-meteo.com/v1/forecast"

    try:
        # 1. Geocoding API to get latitude and longitude
        geocoding_params = {"name": city_name, "count": 1}
        geocoding_response = requests.get(geocoding_url, params=geocoding_params)
        geocoding_response.raise_for_status()  # Raise an exception for HTTP errors
        geocoding_data = geocoding_response.json()

        if not geocoding_data or not geocoding_data.get("results"):
            print(f"Error: City '{city_name}' not found.")
            return None

        latitude = geocoding_data["results"][0]["latitude"]
        longitude = geocoding_data["results"][0]["longitude"]
        city = geocoding_data["results"][0]["name"]

        # 2. Forecast API to get weather data using coordinates
        forecast_params = {
            "latitude": latitude,
            "longitude": longitude,
            "hourly": ["temperature_2m", "relativehumidity_2m", "windspeed_10m", "weathercode"],
            "current_weather": True
        }
        forecast_response = requests.get(forecast_url, params=forecast_params)
        forecast_response.raise_for_status()  # Raise an exception for HTTP errors
        forecast_data = forecast_response.json()

        current_weather = forecast_data.get("current_weather")
        hourly_data = forecast_data.get("hourly")
        hourly_units = forecast_data.get("hourly_units")

        if not current_weather or not hourly_data or not hourly_units:
            print("Error: Could not retrieve current weather data.")
            return None

        # 3. Map weathercode to description (simplified for brevity)
        weather_code = current_weather.get("weathercode")
        weather_conditions = {
            0: "Clear sky",
            1: "Mainly clear",
            2: "Partly cloudy",
            3: "Overcast",
            45: "Fog",
            48: "Depositing rime fog",
            51: "Drizzle: Light intensity",
            53: "Drizzle: Moderate intensity",
            55: "Drizzle: Dense intensity",
            56: "Freezing Drizzle: Light intensity",
            57: "Freezing Drizzle: Dense intensity",
            61: "Rain: Slight intensity",
            63: "Rain: Moderate intensity",
            65: "Rain: Heavy intensity",
            66: "Freezing Rain: Light intensity",
            67: "Freezing Rain: Heavy intensity",
            71: "Snow fall: Slight intensity",
            73: "Snow fall: Moderate intensity",
            75: "Snow fall: Heavy intensity",
            77: "Snow grains",
            80: "Rain showers: Slight intensity",
            81: "Rain showers: Moderate intensity",
            82: "Rain showers: Violent intensity",
            85: "Snow showers slight",
            86: "Snow showers heavy",
            95: "Thunderstorm: Slight or moderate",
            96: "Thunderstorm with slight hail",
            99: "Thunderstorm with heavy hail",
        }
        weather_condition = weather_conditions.get(weather_code, "Unknown")

        # 4. Return the weather data
        weather_info = {
            "city": city,
            "latitude": latitude,
            "longitude": longitude,
            "temperature_celsius": current_weather.get("temperature"),
            "relative_humidity_percent": hourly_data.get("relativehumidity_2m", [])[0] if hourly_data.get("relativehumidity_2m") else None,
            "wind_speed_ms": current_weather.get("windspeed"),
            "weather_condition": weather_condition,
        }
        return weather_info

    except requests.exceptions.RequestException as e:
        print(f"Error: Network issue occurred - {e}")
        return None
    except requests.exceptions.HTTPError as e:
        print(f"Error: HTTP error occurred - {e}")
        return None
    except ValueError as e:
        print(f"Error: Invalid JSON response - {e}")
        return None
    except KeyError as e:
        print(f"Error: Could not find expected data in the API response - {e}")
        return None
    except Exception as e:
        print(f"An unexpected error occurred: {e}")
        return None

if __name__ == "__main__":
    city = "Alexandria, Virginia"
    weather = get_weather_data(city)
    if weather:
        print(f"Current weather in {weather['city']}:")
        print(f"  Latitude: {weather['latitude']}")
        print(f"  Longitude: {weather['longitude']}")
        print(f"  Temperature: {weather['temperature_celsius']} °C")
        print(f"  Humidity: {weather['relative_humidity_percent']}%")
        print(f"  Wind Speed: {weather['wind_speed_ms']} m/s")
        print(f"  Condition: {weather['weather_condition']}")
    else:
        print("Failed to retrieve weather data.")

    city_not_found = "NonExistentCityName"
    weather_not_found = get_weather_data(city_not_found)
    if not weather_not_found:
        print(f"\nAttempted to get weather for '{city_not_found}' and it failed as expected.")

import requests

def get_weather_data(city_name):
    """
    Retrieves current weather data for a given city using the Open-Meteo API.

    Args:
        city_name (str): The name of the city.

    Returns:
        dict or None: A dictionary containing the current weather information
                     or None if an error occurs.
    """
    geocoding_url = "https://geocoding-api.open-meteo.com/v1/search"
    forecast_url = "https://api.open-meteo.com/v1/forecast"

    try:
        # 1. Geocoding API to get latitude and longitude
        geocoding_params = {"name": city_name, "count": 1}
        geocoding_response = requests.get(geocoding_url, params=geocoding_params)
        geocoding_response.raise_for_status()  # Raise an exception for HTTP errors
        geocoding_data = geocoding_response.json()

        if not geocoding_data or not geocoding_data.get("results"):
            print(f"Error: City '{city_name}' not found.")
            return None

        latitude = geocoding_data["results"][0]["latitude"]
        longitude = geocoding_data["results"][0]["longitude"]
        city = geocoding_data["results"][0]["name"]

        # 2. Forecast API to get weather data using coordinates
        forecast_params = {
            "latitude": latitude,
            "longitude": longitude,
            "hourly": ["temperature_2m", "relativehumidity_2m", "windspeed_10m", "weathercode"],
            "current_weather": True
        }
        forecast_response = requests.get(forecast_url, params=forecast_params)
        forecast_response.raise_for_status()  # Raise an exception for HTTP errors
        forecast_data = forecast_response.json()

        current_weather = forecast_data.get("current_weather")
        hourly_data = forecast_data.get("hourly")
        hourly_units = forecast_data.get("hourly_units")

        if not current_weather or not hourly_data or not hourly_units:
            print("Error: Could not retrieve current weather data.")
            return None

        # 3. Map weathercode to description (simplified for brevity)
        weather_code = current_weather.get("weathercode")
        weather_conditions = {
            0: "Clear sky",
            1: "Mainly clear",
            2: "Partly cloudy",
            3: "Overcast",
            45: "Fog",
            48: "Depositing rime fog",
            51: "Drizzle: Light intensity",
            53: "Drizzle: Moderate intensity",
            55: "Drizzle: Dense intensity",
            56: "Freezing Drizzle: Light intensity",
            57: "Freezing Drizzle: Dense intensity",
            61: "Rain: Slight intensity",
            63: "Rain: Moderate intensity",
            65: "Rain: Heavy intensity",
            66: "Freezing Rain: Light intensity",
            67: "Freezing Rain: Heavy intensity",
            71: "Snow fall: Slight intensity",
            73: "Snow fall: Moderate intensity",
            75: "Snow fall: Heavy intensity",
            77: "Snow grains",
            80: "Rain showers: Slight intensity",
            81: "Rain showers: Moderate intensity",
            82: "Rain showers: Violent intensity",
            85: "Snow showers slight",
            86: "Snow showers heavy",
            95: "Thunderstorm: Slight or moderate",
            96: "Thunderstorm with slight hail",
            99: "Thunderstorm with heavy hail",
        }
        weather_condition = weather_conditions.get(weather_code, "Unknown")

        # 4. Return the weather data
        weather_info = {
            "city": city,
            "latitude": latitude,
            "longitude": longitude,
            "temperature_celsius": current_weather.get("temperature"),
            "relative_humidity_percent": hourly_data.get("relativehumidity_2m", [])[0] if hourly_data.get("relativehumidity_2m") else None,
            "wind_speed_ms": current_weather.get("windspeed"),
            "weather_condition": weather_condition,
        }
        return weather_info

    except requests.exceptions.RequestException as e:
        print(f"Error: Network issue occurred - {e}")
        return None
    except requests.exceptions.HTTPError as e:
        print(f"Error: HTTP error occurred - {e}")
        return None
    except ValueError as e:
        print(f"Error: Invalid JSON response - {e}")
        return None
    except KeyError as e:
        print(f"Error: Could not find expected data in the API response - {e}")
        return None
    except Exception as e:
        print(f"An unexpected error occurred: {e}")
        return None

if __name__ == "__main__":
    city = "Alexandria, Virginia"
    weather = get_weather_data(city)
    if weather:
        print(f"Current weather in {weather['city']}:")
        print(f"  Latitude: {weather['latitude']}")
        print(f"  Longitude: {weather['longitude']}")
        print(f"  Temperature: {weather['temperature_celsius']} °C")
        print(f"  Humidity: {weather['relative_humidity_percent']}%")
        print(f"  Wind Speed: {weather['wind_speed_ms']} m/s")
        print(f"  Condition: {weather['weather_condition']}")
    else:
        print("Failed to retrieve weather data.")

    city_not_found = "NonExistentCityName"
    weather_not_found = get_weather_data(city_not_found)
    if not weather_not_found:
        print(f"\nAttempted to get weather for '{city_not_found}' and it failed as expected.")

Reply to post from Johnny Tyson IIIReply
Like post from Johnny Tyson III LikeLike count: 0
Mark as unreadMark as unread
TL
Tim Lau
1 Jun 12:50

Reply from Tim Lau
Here is the code in a single HTML file


<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Weather App</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
        }
        h1 {
            color: #333;
        }
        form {
            margin-bottom: 20px;
        }
        #weather-display {
            margin-top: 20px;
            font-size: 18px;
        }
    </style>
</head>
<body>
    <h1>Weather App</h1>
    <form id="weather-form">
        <input type="text" id="city-input" placeholder="Enter city name" required>
        <button type="submit">Get Weather</button>
    </form>
    <div id="weather-display"></div>
    <script>
        const weatherDisplay = document.getElementById('weather-display'); // Declare globally

        document.addEventListener('DOMContentLoaded', () => {
            const form = document.getElementById('weather-form');
            const cityInput = document.getElementById('city-input');

            form.addEventListener('submit', async (event) => {
                event.preventDefault();
                const city = cityInput.value.trim();
                if (city) {
                    try {
                        const coordinates = await fetchCityCoordinates(city);
                        const weatherData = await fetchWeatherData(coordinates.latitude, coordinates.longitude);
                        displayTemperature(weatherData);
                    } catch (error) {
                        weatherDisplay.innerHTML = `<p>Error: ${error.message}</p>`;
                    }
                }
            });
        });

        async function fetchCityCoordinates(city) {
            const response = await fetch(`https://geocoding-api.open-meteo.com/v1/search?name=${encodeURIComponent(city)}`);
            if (!response.ok) {
                throw new Error('Failed to fetch city coordinates');
            }
            const data = await response.json();
            if (!data.results || data.results.length === 0) {
                throw new Error('City not found');
            }

            // Check if the first result matches the input city name exactly
            const match = data.results.find(result => result.name.toLowerCase() === city.toLowerCase());
            if (!match) {
                throw new Error('City not found');
            }

            const { latitude, longitude } = match;
            return { latitude, longitude };
        }

        async function fetchWeatherData(latitude, longitude) {
            const response = await fetch(`https://api.open-meteo.com/v1/forecast?latitude=${latitude}&longitude=${longitude}&current_weather=true`);
            if (!response.ok) {
                throw new Error('Failed to fetch weather data');
            }
            return await response.json();
        }

        function displayTemperature(data) {
            const { temperature } = data.current_weather;
            weatherDisplay.innerHTML = `<p>Temperature: ${temperature}°C</p>`;
        }
    </script>
</body>
</html>
Reply to post from Tim LauReply
Like post from Tim Lau LikeLike count: 0
Mark as unreadMark as unread
Deleted by Tim Lau
Deleted 1 Jun 12:51
BC
Betilieth Crutchfield
31 May 23:49

Reply from Betilieth Crutchfield
 

My code for the weather app.

import requests

def get_coordinates(city):
    geo_url = "https://geocoding-api.open-meteo.com/v1/search"
    params = {
        "name": city,
        "count": 1,
        "language": "en",
        "format": "json"
    }
    response = requests.get(geo_url, params=params)
    data = response.json()

    if not data.get("results"):
        raise ValueError("City not found.")
    
    lat = data["results"][0]["latitude"]
    lon = data["results"][0]["longitude"]
    return lat, lon

def get_temperature(lat, lon):
    weather_url = "https://api.open-meteo.com/v1/forecast"
    params = {
        "latitude": lat,
        "longitude": lon,
        "current_weather": True
    }
    response = requests.get(weather_url, params=params)
    data = response.json()
    return data["current_weather"]["temperature"]

def main():
    city = input("Enter a city name: ")
    try:
        lat, lon = get_coordinates(city)
        temp = get_temperature(lat, lon)
        print(f"The current temperature in {city} is {temp}°C.")
    except ValueError as ve:
        print(f"Error: {ve}")
    except Exception as e:
        print("An unexpected error occurred:", e)

if __name__ == "__main__":
    main()

Reply to post from Betilieth CrutchfieldReply
Like post from Betilieth Crutchfield LikeLike count: 0
Mark as unreadMark as unread
Kibrom Anbessie
31 May 20:17

Reply from Kibrom Anbessie
  public static Map<String, Object> getCityWeather(String city) {
        try {
            String geoApiBase = System.getenv("GEOCODING_API_BASE");
            String weatherApiBase = System.getenv("WEATHER_API_BASE");

            String geoUrl = String.format("%s?name=%s&count=1", geoApiBase, URI.create(city).toASCIIString());
            HttpRequest geoRequest = HttpRequest.newBuilder()
                    .uri(URI.create(geoUrl))
                    .GET()
                    .build();

            HttpResponse<String> geoResponse = client.send(geoRequest, HttpResponse.BodyHandlers.ofString());

            if (geoResponse.statusCode() != 200) {
                return Map.of("error", "Failed to fetch geocoding data.");
            }
Reply to post from Kibrom AnbessieReply
Unlike post from Kibrom Anbessie1 LikeLike count: 1
Mark as unreadMark as unread
SK
Sirisha Kantumahonti
31 May 16:48

Reply from Sirisha Kantumahonti
I have used both TabNine and QodoGen. 

And here is the code with imports 

import requests
import json

def get_weather(city):
    # Base URL for the Open-Meteo API
    base_url = "https://api.open-meteo.com/v1/forecast"
   
    # Get latitude and longitude for the city (simplified for this example)
    # In a real app, you'd use a geocoding service to get accurate coordinates
    coordinates = {
        "New York": (40.71, -74.01),
        "London": (51.51, -0.13),
        "Tokyo": (35.68, 139.65),
        "Sydney": (-33.87, 151.21),
        # Add more cities as needed
    }
   
    if city not in coordinates:
        return "City not found in our database."
   
    lat, lon = coordinates[city]
   
    # Parameters for the API request
    params = {
        "latitude": lat,
        "longitude": lon,
        "current_weather": True,
        "temperature_unit": "celsius",
        "windspeed_unit": "kmh",
        "precipitation_unit": "mm"
    }
   
    # Make the API request
    response = requests.get(base_url, params=params)
   
    if response.status_code == 200:
        data = response.json()
        current_weather = data['current_weather']
       
        # Format the weather information
        weather_info = f"""
Weather in {city}:
Temperature: {current_weather['temperature']}°C
Wind Speed: {current_weather['windspeed']} km/h
Wind Direction: {current_weather['winddirection']}°
Weather Code: {current_weather['weathercode']}
        """
        return weather_info
    else:
        return "Failed to fetch weather data."

def main():
    print("Welcome to the Weather App!")
    while True:
        city = input("Enter a city name (or 'quit' to exit): ")
        if city.lower() == 'quit':
            break
        weather_info = get_weather(city)
        print(weather_info)
        print()

if __name__ == "__main__":
    main()
With both AIs we could able to do, but the file structures vary. Qodo Gen gave directly the file and TabNine gave details how to setu the folder structure in detail.
Reply to post from Sirisha KantumahontiReply
Like post from Sirisha Kantumahonti LikeLike count: 0
Mark as unreadMark as unread
Akilah Witherspoon
31 May 11:34

Reply from Akilah Witherspoon
Most of the programs generated similar code. The difference is that Github Copilot used getTemperature() instead of getWeather(). I asked copilot what the difference was and it said 

The difference between getTemperature() and getWeather() is mostly in what information they provide:

getTemperature():
Typically, this function would only fetch and display the current temperature for a city.

getWeather():
This function can fetch and display more comprehensive weather data, such as temperature, wind speed, humidity, weather conditions, etc.

----------Code ----------

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Simple Weather App</title>
<style>
body { font-family: Arial, sans-serif; margin: 40px; }
.container { max-width: 400px; margin: auto; padding: 20px; border: 1px solid #ddd; border-radius: 8px; }
input, button { padding: 8px; font-size: 1em; }
#result { margin-top: 20px; font-size: 1.2em; }
</style>
</head>
<body>
<div class="container">
<h2>Weather App</h2>
<input type="text" id="cityInput" placeholder="Enter city name" />
<button onclick="getWeather()">Get Temperature</button>
<div id="result"></div>
</div>
<script>
async function getWeather() {
const city = document.getElementById('cityInput').value.trim();
const resultDiv = document.getElementById('result');
if (!city) {
resultDiv.textContent = 'Please enter a city name.';
return;
}
resultDiv.textContent = 'Loading...';
try {
// Use Open-Meteo Geocoding API to get latitude and longitude
const geoRes = await fetch(`https://geocoding-api.open-meteo.com/v1/search?name=${encodeURIComponent(city)}&count=1`);
const geoData = await geoRes.json();
if (!geoData.results || geoData.results.length === 0) {
resultDiv.textContent = 'City not found.';
return;
}
const { latitude, longitude, name, country } = geoData.results[0];
// Fetch current weather
const weatherRes = await fetch(`https://api.open-meteo.com/v1/forecast?latitude=${latitude}&longitude=${longitude}&current_weather=true`);
const weatherData = await weatherRes.json();
if (!weatherData.current_weather) {
resultDiv.textContent = 'Weather data not available.';
return;
}
const temp = weatherData.current_weather.temperature;
resultDiv.textContent = `Current temperature in ${name}, ${country}: ${temp}°C`;
} catch (error) {
resultDiv.textContent = 'Error fetching weather data.';
}
}
</script>
</body>
</html>



Reply to post from Akilah WitherspoonReply
Like post from Akilah Witherspoon LikeLike count: 0
Mark as unreadMark as unread
YV
Yogesh Vajpayee (He/Him)
31 May 6:19

Reply from Yogesh Vajpayee
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.io.IOException;
import java.net.HttpURLConnection;
import java.net.URL;
import java.util.Scanner;

import org.json.JSONArray;
import org.json.JSONObject;

public class WeatherApp {

    // Get latitude and longitude from city name using Open-Meteo's geocoding API
    public static double[] getCoordinates(String city) throws IOException {
        String urlString = "https://geocoding-api.open-meteo.com/v1/search?name=" + city;
        URL url = new URL(urlString);
        HttpURLConnection conn = (HttpURLConnection) url.openConnection();
        conn.setRequestMethod("GET");

        BufferedReader in = new BufferedReader(new InputStreamReader(conn.getInputStream()));
        StringBuilder response = new StringBuilder();
        String inputLine;

        while ((inputLine = in.readLine()) != null) {
            response.append(inputLine);
        }

        in.close();
        JSONObject json = new JSONObject(response.toString());

        JSONArray results = json.getJSONArray("results");
        if (results.length() == 0) {
            throw new IOException("City not found");
        }

        JSONObject cityData = results.getJSONObject(0);
        double latitude = cityData.getDouble("latitude");
        double longitude = cityData.getDouble("longitude");

        return new double[]{latitude, longitude};
    }

    // Get current temperature for given coordinates using Open-Meteo API
    public static double getCurrentTemperature(double latitude, double longitude) throws IOException {
        String urlString = "https://api.open-meteo.com/v1/forecast?latitude=" + latitude +
                           "&longitude=" + longitude + "&current_weather=true";
        URL url = new URL(urlString);
        HttpURLConnection conn = (HttpURLConnection) url.openConnection();
        conn.setRequestMethod("GET");

        BufferedReader in = new BufferedReader(new InputStreamReader(conn.getInputStream()));
        StringBuilder response = new StringBuilder();
        String inputLine;

        while ((inputLine = in.readLine()) != null) {
            response.append(inputLine);
        }

        in.close();
        JSONObject json = new JSONObject(response.toString());
        JSONObject currentWeather = json.getJSONObject("current_weather");
        return currentWeather.getDouble("temperature");
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        System.out.print("Enter a city name: ");
        String city = scanner.nextLine();

        try {
            double[] coords = getCoordinates(city);
            double temp = getCurrentTemperature(coords[0], coords[1]);
            System.out.println("Current temperature in " + city + ": " + temp + "°C");
        } catch (IOException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
}

Reply to post from Yogesh VajpayeeReply
Like post from Yogesh Vajpayee LikeLike count: 0
Mark as unreadMark as unread
CS
Chanchal Kumari Singh
30 May 22:32

Reply from Chanchal Kumari Singh
import requests

# Replace with your OpenWeatherMap API key
API_KEY = "your_api_key_here"
BASE_URL = "http://api.openweathermap.org/data/2.5/weather"

def get_weather(city):
    params = {
        "q": city,
        "appid": API_KEY,
        "units": "imperial"  # Use "metric" for Celsius
    }
    response = requests.get(BASE_URL, params=params)
    
    if response.status_code == 200:
        data = response.json()
        temp = data["main"]["temp"]
        weather = data["weather"][0]["description"]
        print(f"The current temperature in {city} is {temp}°F with {weather}.")
    else:
        print("Error fetching weather data. Please check the city name or API key.")

# Example usage
city_name = input("Enter city name: ")
get_weather(city_name)

Reply to post from Chanchal Kumari SinghReply
Unlike post from Chanchal Kumari Singh1 LikeLike count: 1
Mark as unreadMark as unread
John Caputo
30 May 21:12
| Last edited 30 May 21:17

Reply from John Caputo
I used the code from Qodo in Python. The Qodo code looked the best to me, due to being compact and very  readable.

------------------------------------------------------------------

import requests

def get_coordinates(city):
    url = f"https://geocoding-api.open-meteo.com/v1/search?name={city}&count=1"
    response = requests.get(url)
    data = response.json()
    if "results" in data and data["results"]:
        lat = data["results"][0]["latitude"]
        lon = data["results"][0]["longitude"]
        return lat, lon
    else:
        return None, None

def get_temperature(lat, lon):
    url = f"https://api.open-meteo.com/v1/forecast?latitude={lat}&longitude={lon}&current_weather=true"
    response = requests.get(url)
    data = response.json()
    if "current_weather" in data:
        return data["current_weather"]["temperature"]
    else:
        return None

def main():
    city = input("Enter city name: ")
    lat, lon = get_coordinates(city)
    if lat is None or lon is None:
        print("City not found.")
        return
    temp = get_temperature(lat, lon)
    if temp is not None:
        print(f"The current temperature in {city} is {temp}°C.")
    else:
        print("Could not retrieve temperature data.")

if __name__ == "__main__":
    main()

 

NO CHANGES

Reply to post from John CaputoReply
Like post from John Caputo LikeLike count: 0
Mark as unreadMark as unread
Mark post as readTM
Teagan Murray
30 May 21:09

Reply from Teagan Murray
I ended up using Copilot's suggested code.  I asked it for help problem solving (there was an issue with the script line linked to the js file).  And then once it was working I asked it to add in some code to alter the background colour based on the temperature, testing out it's ability to add into the code iteself (seems scary to use, but it worked and was insanely quick).

JS:

async function getWeather() {
  const city = document.getElementById('cityInput').value;
  const resultElement = document.getElementById('result');

  try {
    // Step 1: Geocoding API to get coordinates
    const geoRes = await fetch(`https://geocoding-api.open-meteo.com/v1/search?name=${encodeURIComponent(city)}&count=1`);
    const geoData = await geoRes.json();

    if (!geoData.results || geoData.results.length === 0) {
      resultElement.textContent = 'City not found.';
      document.body.style.backgroundColor = ""; // Reset background
      return;
    }

    const { latitude, longitude } = geoData.results[0];

    // Step 2: Weather API to get current temperature
    const weatherRes = await fetch(`https://api.open-meteo.com/v1/forecast?latitude=${latitude}&longitude=${longitude}&current_weather=true`);
    const weatherData = await weatherRes.json();

    const temperature = weatherData.current_weather.temperature;
    resultElement.textContent = `Current temperature in ${city}: ${temperature}°C`;
    document.body.style.backgroundColor = temperatureToColor(temperature);
  } catch (error) {
    console.error(error);
    resultElement.textContent = 'Error fetching weather data.';
    document.body.style.backgroundColor = ""; // Reset background
  }
}

function temperatureToColor(temp) {
  // Clamp temp between 0 and 50
  temp = Math.max(0, Math.min(50, temp));
  // Interpolate: 0°C = rgb(0,0,139) (dark blue), 50°C = rgb(255,0,0) (red)
  const r = Math.round((temp / 50) * 255);
  const g = 0;
  const b = Math.round(139 * (1 - temp / 50));
  return `rgb(${r},${g},${b})`;
}
HTML:
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Weather App</title>
</head>
<body>
  <h1>Simple Weather App</h1>
  <input type="text" id="cityInput" placeholder="Enter city name" />
  <button onclick="getWeather()">Get Weather</button>
  <p id="result"></p>

  <script src="weather-app/src/weather.js"></script>
</body>
</html>
Reply to post from Teagan MurrayReply
Like post from Teagan Murray LikeLike count: 0
Mark as readMark as read
Mark post as readMK
MD IMRAN KHAN
30 May 10:11

Reply from MD IMRAN KHAN
Here’s a simple weather app using React and the OpenWeatherMap API. It allows users to enter a city and get the current weather data.

🔧 Prerequisites
Node.js & npm installed

API key from OpenWeatherMap

📁 Folder Structure
weather-app/
├── public/
│   └── index.html
├── src/
│   ├── App.js
│   ├── Weather.js
│   └── index.js
├── .env
├── package.json

1. .env
Store your OpenWeatherMap API key securely.

env
REACT_APP_WEATHER_API_KEY=your_openweathermap_api_key_here
2. App.js
import React, { useState } from "react";
import Weather from "./Weather";

function App() {
  const [city, setCity] = useState("");
  const [weather, setWeather] = useState(null);
  const [error, setError] = useState("");

  const getWeather = async () => {
    try {
      setError("");
      const apiKey = process.env.REACT_APP_WEATHER_API_KEY;
      const res = await fetch(
        `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${apiKey}&units=metric`
      );
      if (!res.ok) throw new Error("City not found");
      const data = await res.json();
      setWeather(data);
    } catch (err) {
      setWeather(null);
      setError(err.message);
    }
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    if (city.trim() !== "") {
      getWeather();
    }
  };

  return (
    <div style={styles.container}>
      <h1>🌤 Weather App</h1>
      <form onSubmit={handleSubmit}>
        <input
          type="text"
          placeholder="Enter city"
          value={city}
          onChange={(e) => setCity(e.target.value)}
          style={styles.input}
        />
        <button type="submit" style={styles.button}>Get Weather</button>
      </form>
      {error && <p style={styles.error}>{error}</p>}
      {weather && <Weather data={weather} />}
    </div>
  );
}

const styles = {
  container: { textAlign: "center", padding: "2rem", fontFamily: "Arial" },
  input: { padding: "0.5rem", fontSize: "1rem", marginRight: "0.5rem" },
  button: { padding: "0.5rem 1rem", fontSize: "1rem" },
  error: { color: "red", marginTop: "1rem" }
};

export default App;

3. Weather.js
jsx
import React from "react";

function Weather({ data }) {
  return (
    <div style={styles.card}>
      <h2>{data.name}, {data.sys.country}</h2>
      <p>{data.weather[0].description}</p>
      <p>🌡 {data.main.temp}°C</p>
      <p>💨 Wind: {data.wind.speed} m/s</p>
    </div>
  );
}

const styles = {
  card: {
    marginTop: "1rem",
    padding: "1rem",
    border: "1px solid #ccc",
    borderRadius: "10px",
    display: "inline-block",
    backgroundColor: "#f3f3f3"
  }
};

export default Weather;

4. index.js
jsx
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App";

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<App />);

5. Install & Run
bash
npm install
npm start
Reply to post from MD IMRAN KHANReply
Like post from MD IMRAN KHAN LikeLike count: 0
Mark as readMark as read
Mark post as readNH
Noor Hafizah Mohamad Hamdan
30 May 7:20
| Last reply 31 May 16:50

Reply from Noor Hafizah Mohamad Hamdan
import requests

def get_weather(city):
    try:
        # Step 1: Geocoding
        geo_url = f"https://geocoding-api.open-meteo.com/v1/search?name={city}&count=1"
        geo_response = requests.get(geo_url)
        geo_response.raise_for_status()
        geo_data = geo_response.json()

        if "results" not in geo_data or not geo_data["results"]:
            return {"error": "City not found"}

        lat = geo_data["results"][0]["latitude"]
        lon = geo_data["results"][0]["longitude"]

        # Step 2: Fetch current weather
        weather_url = (
            f"https://api.open-meteo.com/v1/forecast?"
            f"latitude={lat}&longitude={lon}&current_weather=true"
        )
        weather_response = requests.get(weather_url)
        weather_response.raise_for_status()
        weather_data = weather_response.json()

        current = weather_data.get("current_weather", {})
        if not current:
            return {"error": "Weather data unavailable"}

        return {
            "city": city,
            "temperature": current.get("temperature"),
            "weather_code": current.get("weathercode"),
            "windspeed": current.get("windspeed")
        }

    except requests.exceptions.RequestException as e:
        return {"error": f"Network error: {e}"}

 

Check Against API Docs
From Open-Meteo documentation:

✔️ Geocoding API endpoint: https://geocoding-api.open-meteo.com/v1/search

✔️ Weather API endpoint: https://api.open-meteo.com/v1/forecast

✔️ Required parameters: latitude, longitude, current_weather=true

The function satisfies:

Coordinates, not city name usage

Latest version of the API

Proper error handling

Analyse the Output
Correctness: ✅ Yes, it converts city names to coordinates and fetches weather data

Error handling: ✅ Yes, for invalid cities and network issues

Structure & Readability: ✅ Clean and well-structured

API best practices: ✅ Uses .raise_for_status(), checks JSON fields

Input/Output: ✅ User-friendly input, structured dictionary output

 

Refine (Optional)
To improve, you could:

Map weather codes to human-readable descriptions (Open-Meteo provides a table)

Add Celsius ↔ Fahrenheit conversion

Format the output as a user-friendly sentence

 

Test
Run the function in your Python environment:

print(get_weather("Singapore"))
print(get_weather("InvalidCity123"))

Summary: TRACI Breakdown
Component	Your Prompt Included?	Example
Task	✅	Get weather for a city
Role	✅	Python with requests
Audience	✅	Beginner-friendly
Create	✅	Output is a dictionary
Intent	✅	Includes error handling, latest API
Expand discussion thread from Noor Hafizah Mohamad Hamdan
1 Reply, 1 Unread
1 Reply, 1 Unread
Reply to post from Noor Hafizah Mohamad HamdanReply
Like post from Noor Hafizah Mohamad Hamdan LikeLike count: 0
Mark as readMark as read
Mark post as readJG
James Gordon
30 May 4:14

Reply from James Gordon
import requests

def get_coordinates(city):
    # Use Open-Meteo's free geocoding API to get latitude and longitude
    url = f"https://geocoding-api.open-meteo.com/v1/search?name={city}&count=1"
    response = requests.get(url)
    data = response.json()
    
    if "results" in data and len(data["results"]) > 0:
        lat = data["results"][0]["latitude"]
        lon = data["results"][0]["longitude"]
        return lat, lon
    else:
        return None, None

def get_temperature(lat, lon):
    # Fetch current weather data
    url = f"https://api.open-meteo.com/v1/forecast?latitude={lat}&longitude={lon}&current_weather=true"
    response = requests.get(url)
    data = response.json()
    
    if "current_weather" in data:
        return data["current_weather"]["temperature"]
    else:
        return None

def main():
    city = input("Enter a city name: ")
    lat, lon = get_coordinates(city)
    
    if lat is None or lon is None:
        print(f"Sorry, could not find coordinates for '{city}'.")
        return
    
    temperature = get_temperature(lat, lon)
    if temperature is not None:
        print(f"The current temperature in {city} is {temperature}°C.")
    else:
        print("Sorry, could not fetch the temperature data.")

if __name__ == "__main__":
    main()

Reply to post from James GordonReply
Like post from James Gordon LikeLike count: 0
Mark as readMark as read
Mark post as readEG
Eyobel Gebremichael
29 May 23:58

Reply from Eyobel Gebremichael
const readline = require('readline');
const fetch = require('node-fetch');

// Set up input interface
const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout
});

// Get city name from user
rl.question('Enter a city name: ', async (city) => {
  try {
    // Step 1: Get coordinates from Open-Meteo Geocoding API
    const geoUrl = `https://geocoding-api.open-meteo.com/v1/search?name=${encodeURIComponent(city)}&count=1`;
    const geoResponse = await fetch(geoUrl);
    const geoData = await geoResponse.json();

    if (!geoData.results || geoData.results.length === 0) {
      console.log('City not found.');
      rl.close();
      return;
    }

    const { latitude, longitude } = geoData.results[0];

    // Step 2: Get weather data from Open-Meteo
    const weatherUrl = `https://api.open-meteo.com/v1/forecast?latitude=${latitude}&longitude=${longitude}&current_weather=true`;
    const weatherResponse = await fetch(weatherUrl);
    const weatherData = await weatherResponse.json();

    const temperature = weatherData.current_weather.temperature;
    console.log(`Current temperature in ${city} is ${temperature}°C`);
  } catch (error) {
    console.error('Error fetching weather data:', error);
  } finally {
    rl.close();
  }
});
Reply to post from Eyobel GebremichaelReply
Like post from Eyobel Gebremichael LikeLike count: 0
Mark as readMark as read
Mark post as readSM
Siedah McAlpine
29 May 17:12
| Last edited 29 May 17:13
| Last reply 30 May 4:16

Reply from Siedah McAlpine
This is the script I used with ChaptGpt, GitHub CoPilot, Tabnine, and Qodo: 

"I’m building a simple weather app in HTML using VS Code. The app should take user input (a city name), fetch weather data from an API like Open-Meteo, and display it in a user-friendly format. Can you suggest a clear and organized project folder structure?"

They all gave me a clear and organized folder structure to make my weather app easier to develop and maintain. They also suggested what files and folders to make in my terminal and codes to add to files in VsCode. However, based on the simplicity of directions and answers (I was playing catch-up, as I have been away from coding for a little bit), as well as the ease of moving between tabs on my computer, I settled on switching between ChatGpt and CoPilot. After asking both AI servers "what are the next steps?" and getting different results on going forward, I asked "Can you help me write a simple script in [your chosen programming language] that takes a city name as input, makes an API request to Open-Meteo, and prints the temperature?", they differed with ChatGpt using Python script and CoPilot using a JavaScript(node.js) script. After asking why they used those specific languages, I had asked ChatGpt to use JS, so I could further compare/contrast with CoPilot. 

*I found switching between ChatGpt and CoPilot very resourceful and helpful in catching up on coding and refamiliarizing myself with everything. However with CoPilot, I had to ask a lot of follow-up questions to better understand what is what, as it initially gave me minimal information, compared to ChatGpt, which gave me more information (however I had to tweak it to more technical results like CoPilot).

The script:

[weather-app/public/js/app.js]

document.getElementById('weather-form').addEventListener('submit', async function (e) {
  e.preventDefault();

  const city = document.getElementById('city').value;
  const resultDiv = document.getElementById('result');
  resultDiv.textContent = "Loading...";

  try {
    const response = await fetch(`/weather?city=${encodeURIComponent(city)}`);
    const data = await response.json();

    if (response.ok) {
      resultDiv.textContent = `Weather in ${data.city}, ${data.country}: ${data.temperature}°C`;
    } else {
      resultDiv.textContent = data.error || "Error fetching weather data.";
    }
  } catch (err) {
    resultDiv.textContent = "Could not retrieve weather data.";
  }
});

Expand discussion thread from Siedah McAlpine
1 Reply, 1 Unread
1 Reply, 1 Unread
Reply to post from Siedah McAlpineReply
Like post from Siedah McAlpine1 LikeLike count: 1
Mark as readMark as read
Mark post as readPR
Pronoy Roy
29 May 14:51

Reply from Pronoy Roy
Entry point:

import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';
import './styles.css'; // optional

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);

Main App Logic:

import React, { useState } from 'react';
import SearchBar from './components/SearchBar';
import WeatherCard from './components/WeatherCard';
import { fetchWeatherByCity } from './services/api';

const App = () => {
  const [city, setCity] = useState('');
  const [weather, setWeather] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState('');

  const handleSearch = async (cityName) => {
    setCity(cityName);
    setLoading(true);
    setError('');
    try {
      const data = await fetchWeatherByCity(cityName);
      setWeather(data);
    } catch (err) {
      setError('Could not fetch weather data. Try again.');
    }
    setLoading(false);
  };

  return (
    <div className="container">
      <h1>Weather App</h1>
      <SearchBar onSearch={handleSearch} />
      {loading && <p>Loading...</p>}
      {error && <p style={{ color: 'red' }}>{error}</p>}
      {weather && <WeatherCard city={city} weather={weather} />}
    </div>
  );
};

export default App;

City input:

import React, { useState } from 'react';

const SearchBar = ({ onSearch }) => {
  const [input, setInput] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();
    if (input.trim()) {
      onSearch(input.trim());
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        placeholder="Enter city name"
        value={input}
        onChange={(e) => setInput(e.target.value)}
      />
      <button type="submit">Get Weather</button>
    </form>
  );
};

export default SearchBar;

Display Weather info:

import React from 'react';

const WeatherCard = ({ city, weather }) => {
  const { temperature, windspeed, weathercode } = weather;
  return (
    <div className="weather-card">
      <h2>{city}</h2>
      <p>Temperature: {temperature}°C</p>
      <p>Wind Speed: {windspeed} km/h</p>
      <p>Weather Code: {weathercode}</p>
    </div>
  );
};

export default WeatherCard;

App call to Open-Meteo:

export const fetchWeatherByCity = async (city) => {
  // 1. Get latitude and longitude
  const geoRes = await fetch(
    `https://geocoding-api.open-meteo.com/v1/search?name=${encodeURIComponent(city)}&count=1`
  );
  const geoData = await geoRes.json();

  if (!geoData.results || geoData.results.length === 0) {
    throw new Error('City not found');
  }

  const { latitude, longitude } = geoData.results[0];

  // 2. Get current weather using coordinates
  const weatherRes = await fetch(
    `https://api.open-meteo.com/v1/forecast?latitude=${latitude}&longitude=${longitude}&current_weather=true`
  );
  const weatherData = await weatherRes.json();

  return weatherData.current_weather;
};

Reply to post from Pronoy RoyReply
Like post from Pronoy Roy LikeLike count: 0
Mark as readMark as read
Mark post as readAM
Abdulkerim Metenea
29 May 14:32

Reply from Abdulkerim Metenea
image.png
import { ApplicationConfig, provideZoneChangeDetection } from '@angular/core';
import { provideRouter } from '@angular/router';
import { provideHttpClient } from '@angular/common/http'; // <-- Add this

import { routes } from './app.routes';

export const appConfig: ApplicationConfig = {
  providers: [
    provideZoneChangeDetection({ eventCoalescing: true }),
    provideRouter(routes),
    provideHttpClient() // <-- Add this
  ]
};
====================
import { Routes } from '@angular/router';
import { WeatherComponent } from './weather/weather.component';

export const routes: Routes = [
  { path: '', component: WeatherComponent }
];
==============================
<div class="weather-container">
  <h2>WEATHER</h2>
  <input
    type="text"
    [(ngModel)]="city"
    placeholder="Enter your city"
    class="city-input"
  />
  <div class="weather-icon">
    <!-- Simple SVG for partly cloudy -->
    <svg width="80" height="80" viewBox="0 0 64 64">
      <circle cx="32" cy="32" r="20" fill="#fff" opacity="0.5"/>
      <circle cx="40" cy="28" r="16" fill="#fff"/>
      <path d="M24 44h24a12 12 0 0 0-24 0z" fill="#ccc"/>
    </svg>
  </div>
  <div class="temperature">{{ temperature }}&deg;</div>
  <div class="description">{{ description }}</div>
  <button (click)="getWeather()" class="search-btn">Search</button>
</div>
=================
.weather-container {
  background: linear-gradient(to bottom, #8ecae6, #219ebc);
  border-radius: 24px;
  padding: 32px;
  max-width: 320px;
  margin: 40px auto;
  text-align: center;
  color: #fff;
  box-shadow: 0 4px 24px rgba(0,0,0,0.08);
}
.city-input {
  width: 90%;
  padding: 12px;
  border-radius: 16px;
  border: none;
  margin-bottom: 24px;
  font-size: 1.1rem;
  text-align: center;
}
.weather-icon {
  margin: 24px 0;
}
.temperature {
  font-size: 3rem;
  font-weight: bold;
}
.description {
  font-size: 1.2rem;
  margin-bottom: 24px;
}
.search-btn {
  background: #023047;
  color: #fff;
  border: none;
  border-radius: 24px;
  padding: 12px 36px;
  font-size: 1.1rem;
  cursor: pointer;
  transition: background 0.2s;
}
.search-btn:hover {
  background: #035388;
}
===================
import { Component } from '@angular/core';
import { HttpClient } from '@angular/common/http';

@Component({
  selector: 'app-weather',
  standalone: true,
  templateUrl: './weather.component.html',
  styleUrls: ['./weather.component.css'],
})
export class WeatherComponent {
  city: string = 'Washington DC';
  temperature: string = '--';
  description: string = 'Loading...';

  constructor(private http: HttpClient) {
    this.getWeather();
  }

  getWeather() {
    // Use Open-Meteo Geocoding API to get coordinates for the city
    this.description = 'Loading...';
    this.temperature = '--';
    this.http
      .get<any>(
        `https://geocoding-api.open-meteo.com/v1/search?name=${encodeURIComponent(
          this.city
        )}&count=1`
      )
      .subscribe((geoData) => {
        if (
          geoData &&
          geoData.results &&
          geoData.results.length > 0
        ) {
          const { latitude, longitude } = geoData.results[0];
          // Now fetch weather for these coordinates
          this.http
            .get<any>(
              `https://api.open-meteo.com/v1/forecast?latitude=${latitude}&longitude=${longitude}&current_weather=true`
            )
            .subscribe((weatherData) => {
              if (
                weatherData &&
                weatherData.current_weather
              ) {
                this.temperature = Math.round(
                  weatherData.current_weather.temperature
                ).toString();
                this.description = this.getWeatherDescription(
                  weatherData.current_weather.weathercode
                );
              } else {
                this.temperature = '--';
                this.description = 'No data';
              }
            });
        } else {
          this.temperature = '--';
          this.description = 'City not found';
        }
      });
  }

  getWeatherDescription(code: number): string {
    // Simple mapping for Open-Meteo weather codes
    const codes: { [key: number]: string } = {
      0: 'Clear sky',
      1: 'Mainly clear',
      2: 'Partly cloudy',
      3: 'Overcast',
      45: 'Fog',
      48: 'Depositing rime fog',
      51: 'Light drizzle',
      53: 'Drizzle',
      55: 'Dense drizzle',
      56: 'Freezing drizzle',
      57: 'Freezing drizzle',
      61: 'Slight rain',
      63: 'Rain',
      65: 'Heavy rain',
      66: 'Freezing rain',
      67: 'Freezing rain',
      71: 'Slight snow',
      73: 'Snow',
      75: 'Heavy snow',
      77: 'Snow grains',
      80: 'Slight showers',
      81: 'Showers',
      82: 'Violent showers',
      85: 'Slight snow showers',
      86: 'Heavy snow showers',
      95: 'Thunderstorm',
      96: 'Thunderstorm + hail',
      99: 'Thunderstorm + heavy hail',
    };
    return codes[code] || 'Unknown';
  }
}
Reply to post from Abdulkerim MeteneaReply
Like post from Abdulkerim Metenea LikeLike count: 0
Mark as readMark as read






   

