```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Live Weather App</title>

  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: Arial, sans-serif;
    }

    body {
      min-height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      padding: 20px;
      background: linear-gradient(135deg, #0f2027, #203a43, #2c5364);
      color: white;
    }

    .card {
      width: 100%;
      max-width: 430px;
      padding: 25px;
      border-radius: 25px;
      text-align: center;
      background: rgba(0, 0, 0, 0.65);
      box-shadow: 0 15px 40px rgba(0,0,0,0.5);
    }

    h1 {
      margin-bottom: 20px;
      color: #7ee8fa;
      font-size: 28px;
    }

    .search {
      display: flex;
      gap: 8px;
      margin-bottom: 20px;
    }

    #cityInput {
      flex: 1;
      min-width: 0;
      padding: 14px;
      border: 0;
      outline: 0;
      border-radius: 25px;
      font-size: 16px;
    }

    #searchBtn {
      padding: 14px 18px;
      border: 0;
      border-radius: 25px;
      background: #00aaff;
      color: white;
      font-weight: bold;
      cursor: pointer;
    }

    #searchBtn:hover {
      background: #0077cc;
    }

    #searchBtn:disabled {
      opacity: 0.6;
    }

    #loading {
      display: none;
      margin: 15px 0;
      color: #7ee8fa;
    }

    #error {
      display: none;
      padding: 12px;
      margin: 15px 0;
      border-radius: 12px;
      background: rgba(255, 0, 0, 0.15);
      color: #ffaaaa;
    }

    #weather {
      display: none;
    }

    #cityName {
      font-size: 28px;
      font-weight: bold;
      margin-top: 10px;
    }

    #countryName {
      margin-top: 5px;
      color: #ccc;
    }

    #icon {
      font-size: 65px;
      margin: 15px 0;
    }

    #temperature {
      font-size: 65px;
      font-weight: bold;
    }

    #condition {
      margin: 10px 0 20px;
      font-size: 19px;
      color: #7ee8fa;
    }

    .details {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 12px;
    }

    .box {
      padding: 17px 10px;
      border-radius: 16px;
      background: rgba(255,255,255,0.1);
    }

    .label {
      color: #bbb;
      font-size: 13px;
      margin-bottom: 7px;
    }

    .value {
      font-size: 18px;
      font-weight: bold;
    }

    #time {
      margin-top: 18px;
      font-size: 12px;
      color: #aaa;
    }

    @media (max-width: 400px) {
      .card {
        padding: 20px 15px;
      }

      .search {
        flex-direction: column;
      }

      #searchBtn {
        width: 100%;
      }
    }
  </style>
</head>

<body>

  <div class="card">

    <h1>☁️ Live Weather</h1>

    <div class="search">
      <input
        id="cityInput"
        type="text"
        placeholder="Enter city name"
        value="Delhi"
      >

      <button id="searchBtn">Search</button>
    </div>

    <div id="loading">
      🌍 Weather loading...
    </div>

    <div id="error">
      ❌ City nahi mila. Please correct city name enter karein.
    </div>

    <div id="weather">

      <div id="cityName">Delhi</div>

      <div id="countryName">India</div>

      <div id="icon">☀️</div>

      <div id="temperature">--°C</div>

      <div id="condition">--</div>

      <div class="details">

        <div class="box">
          <div class="label">💧 Humidity</div>
          <div class="value" id="humidity">--%</div>
        </div>

        <div class="box">
          <div class="label">💨 Wind</div>
          <div class="value" id="wind">-- km/h</div>
        </div>

        <div class="box">
          <div class="label">🌡️ Feels Like</div>
          <div class="value" id="feels">--°C</div>
        </div>

        <div class="box">
          <div class="label">🌧️ Rain</div>
          <div class="value" id="rain">-- mm</div>
        </div>

      </div>

      <div id="time">Updated: --</div>

    </div>

  </div>


  <script>

    const cityInput = document.getElementById("cityInput");
    const searchBtn = document.getElementById("searchBtn");

    const loading = document.getElementById("loading");
    const errorBox = document.getElementById("error");
    const weatherBox = document.getElementById("weather");

    const cityName = document.getElementById("cityName");
    const countryName = document.getElementById("countryName");
    const icon = document.getElementById("icon");
    const temperature = document.getElementById("temperature");
    const condition = document.getElementById("condition");
    const humidity = document.getElementById("humidity");
    const wind = document.getElementById("wind");
    const feels = document.getElementById("feels");
    const rain = document.getElementById("rain");
    const time = document.getElementById("time");


    function weatherText(code) {

      const data = {

        0: ["☀️", "Clear Sky"],

        1: ["🌤️", "Mainly Clear"],
        2: ["⛅", "Partly Cloudy"],
        3: ["☁️", "Overcast"],

        45: ["🌫️", "Fog"],
        48: ["🌫️", "Rime Fog"],

        51: ["🌦️", "Light Drizzle"],
        53: ["🌦️", "Drizzle"],
        55: ["🌧️", "Heavy Drizzle"],

        56: ["🌧️", "Freezing Drizzle"],
        57: ["🌧️", "Heavy Freezing Drizzle"],

        61: ["🌦️", "Light Rain"],
        63: ["🌧️", "Moderate Rain"],
        65: ["🌧️", "Heavy Rain"],

        66: ["🌧️", "Freezing Rain"],
        67: ["🌧️", "Heavy Freezing Rain"],

        71: ["🌨️", "Light Snow"],
        73: ["🌨️", "Snow"],
        75: ["❄️", "Heavy Snow"],

        77: ["❄️", "Snow Grains"],

        80: ["🌦️", "Rain Showers"],
        81: ["🌧️", "Rain Showers"],
        82: ["⛈️", "Heavy Rain Showers"],

        85: ["🌨️", "Snow Showers"],
        86: ["❄️", "Heavy Snow Showers"],

        95: ["⛈️", "Thunderstorm"],
        96: ["⛈️", "Thunderstorm + Hail"],
        99: ["⛈️", "Heavy Thunderstorm + Hail"]

      };

      return data[code] || ["🌍", "Unknown"];
    }


    async function checkWeather() {

      const city = cityInput.value.trim();

      if (!city) {
        alert("Please enter city name.");
        return;
      }

      loading.style.display = "block";
      errorBox.style.display = "none";
      weatherBox.style.display = "none";
      searchBtn.disabled = true;

      try {

        /*
          STEP 1
          City name se latitude/longitude
        */

        const geoURL =
          "https://geocoding-api.open-meteo.com/v1/search" +
          "?name=" + encodeURIComponent(city) +
          "&count=1" +
          "&language=en" +
          "&format=json";


        const geoResponse = await fetch(geoURL);

        if (!geoResponse.ok) {
          throw new Error("Geocoding failed");
        }

        const geoData = await geoResponse.json();


        if (
          !geoData.results ||
          geoData.results.length === 0
        ) {
          throw new Error("City not found");
        }


        const location = geoData.results[0];

        const latitude = location.latitude;
        const longitude = location.longitude;


        /*
          STEP 2
          Weather API
        */

        const weatherURL =
          "https://api.open-meteo.com/v1/forecast" +
          "?latitude=" + latitude +
          "&longitude=" + longitude +
          "&current=temperature_2m,relative_humidity_2m,apparent_temperature,precipitation,weather_code,wind_speed_10m" +
          "&timezone=auto";


        const weatherResponse = await fetch(weatherURL);

        if (!weatherResponse.ok) {
          throw new Error("Weather API failed");
        }

        const weatherData = await weatherResponse.json();


        if (!weatherData.current) {
          throw new Error("Weather data unavailable");
        }


        const current = weatherData.current;


        /*
          STEP 3
          Weather condition
        */

        const weather = weatherText(
          current.weather_code
        );


        /*
          STEP 4
          Screen update
        */

        cityName.textContent =
          location.name || city;

        countryName.textContent =
          location.country || "";


        icon.textContent = weather[0];

        temperature.textContent =
          Math.round(current.temperature_2m) + "°C";


        condition.textContent =
          weather[1];


        humidity.textContent =
          current.relative_humidity_2m + "%";


        wind.textContent =
          Math.round(current.wind_speed_10m) +
          " km/h";


        feels.textContent =
          Math.round(current.apparent_temperature) +
          "°C";


        rain.textContent =
          current.precipitation + " mm";


        time.textContent =
          "Updated: " +
          new Date().toLocaleString();


        loading.style.display = "none";

        weatherBox.style.display = "block";


      } catch (err) {

        console.error(err);

        loading.style.display = "none";

        errorBox.textContent =
          "❌ Weather load nahi ho pa raha. Internet connection aur city name check karein.";

        errorBox.style.display = "block";

      } finally {

        searchBtn.disabled = false;

      }

    }


    /*
      Search button
    */

    searchBtn.addEventListener(
      "click",
      checkWeather
    );


    /*
      Enter key
    */

    cityInput.addEventListener(
      "keydown",
      function(event) {

        if (event.key === "Enter") {
          checkWeather();
        }

      }
    );


    /*
      Page open hote hi Delhi ka weather
    */

    window.addEventListener(
      "load",
      function() {
        checkWeather();
      }
    );

  </script>

</body>
</html>
```
