<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>ParkBuddy Heatmap</title>
    <meta name="viewport" content="initial-scale=1,maximum-scale=1,user-scalable=no">
    
    <!-- ✅ Leaflet.js voor kaarten -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>
    
    <!-- ✅ Heatmap Plugin -->
    <script src="https://unpkg.com/leaflet.heat/dist/leaflet-heat.js"></script>

    <!-- ✅ Firebase SDK (Voor Realtime Database) -->
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database.js"></script>
    
    <style>
        body, html { height: 100%; margin: 0; }
        #map { width: 100%; height: 100vh; }
    </style>
</head>
<body>

    <div id="map"></div>

    <script>
        // ✅ Stap 1: Firebase configureren (VERVANG MET JOUW GEGEVENS)
        const firebaseConfig = {
            databaseURL: "https://parkbuddy-82f2e-default-rtdb.europe-west1.firebasedatabase.app/"
        };
        firebase.initializeApp(firebaseConfig);
        const database = firebase.database();

        // ✅ Stap 2: Leaflet-kaart aanmaken
        var map = L.map('map').setView([52.3676, 4.9041], 13); // Startpositie: Amsterdam

        // ✅ Stap 3: OpenStreetMap-kaartlaag toevoegen
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '&copy; OpenStreetMap contributors'
        }).addTo(map);

        // ✅ Stap 4: Heatmap instellen
        var heatmapData = [];
        var heat = L.heatLayer(heatmapData, {
            radius: 20,
            blur: 15,
            maxZoom: 15
        }).addTo(map);

        // ✅ Stap 5: Realtime updates van Firebase ophalen
        database.ref("meldingen").on("value", (snapshot) => {
            heatmapData.length = 0; // Reset data
            snapshot.forEach((childSnapshot) => {
                let data = childSnapshot.val();
                heatmapData.push([data.lat, data.lng]); // Voeg nieuwe melding toe
            });
            heat.setLatLngs(heatmapData); // Update heatmap
        });
    </script>

</body>
</html>
