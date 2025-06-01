# WeatherApp2025

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
