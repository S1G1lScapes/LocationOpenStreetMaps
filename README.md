<!DOCTYPE html>
<html>
<head>
    <title>Live Location</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>body { margin: 0; } #map { height: 100vh; width: 100vw; }</style>
</head>
<body>
    <div id="map"></div>
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script>
        const map = L.map('map').setView([0, 0], 2);
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {attribution: '© OSM'}).addTo(map);

        const latLngs = [];
        const polyline = L.polyline([], {color: 'red'}).addTo(map);
        let marker = null;

        const source = new EventSource('https://ntfy.sh/chaoistaoist-loco/sse');
        source.onmessage = (event) => {
            const data = JSON.parse(event.data);
            if (data.event !== 'message') return;

            // Regex to grab coordinates from your OsmAnd URL
            const match = data.message.match(/pin=([-+]?[0-9]*\.?[0-9]+),([-+]?[0-9]*\.?[0-9]+)/);
            if (match) {
                const newPoint = [parseFloat(match[1]), parseFloat(match[2])];
                latLngs.push(newPoint);
                polyline.setLatLngs(latLngs);
                if (!marker) marker = L.marker(newPoint).addTo(map);
                else marker.setLatLng(newPoint);
                map.setView(newPoint, 15);
            }
        };
    </script>
</body>
</html>
