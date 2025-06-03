# Python_Project
My python project 

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Simple Weather Checker</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css" />
    <style>
        body {
            font-family: Arial, sans-serif;
            background: #f4f4f4;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            flex-direction: column;
        }
        .container {
            background: white;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 0 15px rgba(0,0,0,0.1);
            max-width: 400px;
            width: 100%;
            margin-bottom: 20px;
        }
        .input-group {
            display: flex;
            gap: 10px;
            margin-top: 10px;
        }
        .input-group input {
            flex: 1;
            padding: 10px;
            font-size: 16px;
        }
        .input-group button {
            padding: 10px 20px;
            font-size: 16px;
            white-space: nowrap;
        }
        .weather-info {
            margin-top: 20px;
        }
        .error {
            color: red;
        }
        #map {
            height: 300px;
            width: 100%;
            max-width: 400px;
            border-radius: 10px;
        }
    </style>
</head>
<body>

    <div class="container">
        <h2>Weather Checker</h2>
        <div class="input-group">
            <input type="text" id="cityInput" placeholder="Enter a city name">
            <button onclick="checkWeather()">Check Weather</button>
        </div>
        <div id="result" class="weather-info"></div>
    </div>
    <div id="map"></div>

    <script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>
    <script>
        let map = L.map('map').setView([20, 0], 2); // Default view

        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '© OpenStreetMap contributors'
        }).addTo(map);

        let marker;

        function checkWeather() {
            const city = document.getElementById('cityInput').value;
            const apiKey = '3fd6b777e53be056088835020f81e74a';
            const url = `https://api.openweathermap.org/data/2.5/weather?q=${encodeURIComponent(city)}&appid=${apiKey}&units=metric`;

            const resultDiv = document.getElementById('result');
            resultDiv.innerHTML = "Loading...";

            fetch(url)
                .then(response => {
                    if (!response.ok) {
                        throw new Error('City not found');
                    }
                    return response.json();
                })
                .then(data => {
                    const description = data.weather[0].description;
                    const temperature = data.main.temp;
                    const lat = data.coord.lat;
                    const lon = data.coord.lon;

                    resultDiv.innerHTML = `
                        <p><strong>Weather Description:</strong> ${description}</p>
                        <p><strong>Temperature:</strong> ${temperature}°C</p>
                    `;

                    // Update map view and marker
                    map.setView([lat, lon], 10);
                    if (marker) {
                        marker.setLatLng([lat, lon]);
                    } else {
                        marker = L.marker([lat, lon]).addTo(map);
                    }
                    marker.bindPopup(`${city}`).openPopup();
                })
                .catch(error => {
                    resultDiv.innerHTML = `<p class="error">${error.message}</p>`;
                });
        }
    </script>

</body>
</html>

