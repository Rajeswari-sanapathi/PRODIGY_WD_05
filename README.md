# PRODIGY_WD_05
I have created a web page that fetches weather according to users location or inputed data. It also displays the data of coming next 5 days
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Weather Prediction</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
    <style>
      body {
        background-image: url(i.jpeg);
        background-repeat: no-repeat;
        background-size: cover;
        
      }
      h1 {
        color: hsl(318, 93%, 21%);
        padding: 18px 0;
        text-align: center;
        font-size: 1.75rem;
        
      }
      .weather-input {
        max-width: 500px;
        margin: 0 auto;
      }
      .weather-input input {
        width: 100%;
        height: 46px;
        outline: none;
        padding: 0 17px;
        margin: 10px 0 20px 0;
        font-size: 1.07rem;
        border-radius: 4px;
        border: 1px solid #ccc;
      }
      .weather-input .separator {
        height: 1px;
        width: 100%;
        margin: 25px 0;
        background-color: #bbb;
        display: flex;
        align-items: center;
        justify-content: center;
      }
      .weather-input .separator::before {
        content: "or";
        color: #6C7570;
        font-size: 1.18rem;
        padding: 0 15px;
        background: #E3F2FD;
      }
      .weather-input button {
        width: 100%;
        padding: 10px 0;
        cursor: pointer;
        outline: none;
        border: none;
        color: #FFF;
        font-size: 1rem;
        border-radius: 4px;
        background-color: #5372F0;
      }
      .weather-input .location-btn {
        background-color: darkgray;
      }
      .current-weather {
        color: #fff;
        display: flex;
        border-radius: 5px;
        padding: 20px;
        background: #5372F0;
        justify-content: space-between;
        align-items: center;
        margin-top: 20px;
      }
      .current-weather h2 {
        font-size: 1.7rem;
      }
      .weather-data h4 {
        margin-top: 12px;
        font-size: 1rem;
        font-weight: 500;
      }
      .details {
        flex: 1;
      }
      .icon {
        display: flex;
        flex-direction: column;
        align-items: center;
      }
      .icon img {
        width: 100px;
        height: 100px;
      }
      .days-forecast {
        text-align: center;
        margin-top: 30px;
      }
      .weather-cards {
        display: flex;
        gap: 20px;
        justify-content: center;
        overflow-x: auto;
        padding: 10px;
        white-space: nowrap;
      }
      .weather-cards .card {
        list-style: none;
        color: #fff;
        padding: 18px 16px;
        border-radius: 5px;
        background: #6C7570;
        max-width: 180px;
        text-align: center;
        flex: 0 0 auto;
      }
      .weather-cards .card img {
        max-width: 70px;
        margin: 5px 0;
      }
    </style>
  </head>
  <body>
    <h1>WEATHER PREDICTOR</h1>
    <div class="container">
      <div class="weather-input">
        <h4>Enter a city name</h4>
        <input type="text" placeholder="Enter city" id="cityInput">
        <button class="search-btn" onclick="fetchWeatherByCity()">Search</button>
        <div class="separator"></div>
        <button class="location-btn" onclick="fetchWeatherByLocation()">Use Current Location</button>
      </div>
      <div class="weather-data">
        <div class="current-weather" id="currentWeather">
          <div class="details">
            <h2 id="cityName">City Name</h2>
            <h4 id="temperature">Temperature: -- °C</h4>
            <h4 id="wind">Wind: -- m/s</h4>
            <h4 id="humidity">Humidity: --%</h4>
          </div>
          <div class="icon">
            <img src="C:\Users\DELL\Desktop\weather\weather rainy.jpeg" alt="weather-icon" id="weatherIcon">
            <h4 id="weatherDescription">Description</h4>
          </div>
        </div>
        <div class="days-forecast">
          <h2>5-Day Forecast</h2>
          <ul class="weather-cards" id="forecastCards">
            <!-- Forecast cards will be populated here -->
          </ul>
        </div>
      </div>
    </div>
    <script>
      const apiKey = 'd5894ce5be1729d804515c2f09747851';

      async function fetchWeatherByCity() {
        const city = document.getElementById('cityInput').value;
        if (city) {
          const url = `https://api.openweathermap.org/data/2.5/weather?q=${city}&units=metric&appid=${apiKey}`;
          const forecastUrl = `https://api.openweathermap.org/data/2.5/forecast?q=${city}&units=metric&appid=${apiKey}`;
          fetchWeather(url, forecastUrl);
        }
      }

      async function fetchWeatherByLocation() {
        if (navigator.geolocation) {
          navigator.geolocation.getCurrentPosition(async (position) => {
            const lat = position.coords.latitude;
            const lon = position.coords.longitude;
            const url = `https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&units=metric&appid=${apiKey}`;
            const forecastUrl = `https://api.openweathermap.org/data/2.5/forecast?lat=${lat}&lon=${lon}&units=metric&appid=${apiKey}`;
            fetchWeather(url, forecastUrl);
          });
        } else {
          alert('Geolocation is not supported by this browser.');
        }
      }

      async function fetchWeather(url, forecastUrl) {
        try {
          const weatherResponse = await fetch(url);
          const weatherData = await weatherResponse.json();
          const forecastResponse = await fetch(forecastUrl);
          const forecastData = await forecastResponse.json();

          updateCurrentWeather(weatherData);
          updateForecast(forecastData);
        } catch (error) {
          console.error('Error fetching weather data:', error);
        }
      }

      function updateCurrentWeather(data) {
        document.getElementById('cityName').textContent = `${data.name} (${new Date().toLocaleDateString()})`;
        document.getElementById('temperature').textContent = `Temperature: ${data.main.temp.toFixed(2)} °C`;
        document.getElementById('wind').textContent = `Wind: ${data.wind.speed} m/s`;
        document.getElementById('humidity').textContent = `Humidity: ${data.main.humidity}%`;
        document.getElementById('weatherIcon').src = `http://openweathermap.org/img/wn/${data.weather[0].icon}@2x.png`;
        document.getElementById('weatherDescription').textContent = data.weather[0].description;
      }

      function updateForecast(data) {
        const forecastCards = document.getElementById('forecastCards');
        forecastCards.innerHTML = '';

        // Group forecast by day
        const dailyForecast = {};
        data.list.forEach(item => {
          const date = item.dt_txt.split(' ')[0];
          if (!dailyForecast[date]) {
            dailyForecast[date] = [];
          }
          dailyForecast[date].push(item);
        });

        // Create forecast cards
        Object.keys(dailyForecast).slice(0, 5).forEach(date => {
          const dayData = dailyForecast[date][0];
          const card = document.createElement('li');
          card.className = 'card';
          card.innerHTML = `
            <h3>${date}</h3>
            <img src="http://openweathermap.org/img/wn/${dayData.weather[0].icon}@2x.png" alt="weather-icon">
            <h4>Temperature: ${dayData.main.temp.toFixed(2)} °C</h4>
            <h4>Wind: ${dayData.wind.speed} m/s</h4>
            <h4>Humidity: ${dayData.main.humidity}%</h4>
          `;
          forecastCards.appendChild(card);
        });
      }
    </script>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz" crossorigin="anonymous"></script>
  </body>
</html>

