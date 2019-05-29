<template>
  <div id="lmap" ref="lmap">
  </div>
</template>

<script>
import 'leaflet/dist/leaflet.css';
import "leaflet-draw/dist/leaflet.draw.css";
import "leaflet-toolbar/dist/leaflet.toolbar.css";
import "leaflet-draw-toolbar/dist/leaflet.draw-toolbar.css";
import L from 'leaflet';
import "leaflet-toolbar/dist/leaflet.toolbar-src.js";
import "leaflet-draw/dist/leaflet.draw-src.js"
import "leaflet-draw-toolbar/dist/leaflet.draw-toolbar.js"

export default {
  data() {
    return {
      name: 'leafletMap',
      map: undefined,
      zoom: 11,
      center: [47.31322, 70.83482],      
      color: "#ff00ff",
      lastEvt: undefined,
      selectedRectangleLatLng: undefined
    };
  },
  mounted(){
    this.$nextTick(() => {
      console.log('mounted!');

      //Creating openStreetMap tile layer
      var osmUrl = 'http://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png';
      var osmAttribution =
          'Map data <a target="_blank" href="http://www.openstreetmap.org">OpenStreetMap.org</a> contributors, ' +
          '<a href="http://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>';
      var osmLayer = new L.TileLayer(osmUrl, {
          maxZoom: 18,
          attribution: osmAttribution
          });

      //Creating satellite tile layer
      var satelliteUrl = 
          '<a href="http://www.esri.com/">Esri</a>';
      var satellite_tile = L.tileLayer(
          'http://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}', {
          attribution: '&copy; '+satelliteUrl+', i-cubed, USDA, USGS, AEX, GeoEye, Getmapping, Aerogrid, IGN, IGP, UPR-EGP, and the GIS User Community',
          maxZoom: 18,
          });
      
      //Creating mapbox tile layer
      var mapbox_tile = L.tileLayer('https://api.tiles.mapbox.com/v4/{id}/{z}/{x}/{y}.png?access_token=pk.eyJ1IjoibWFwYm94IiwiYSI6ImNpejY4NXVycTA2emYycXBndHRqcmZ3N3gifQ.rJcFIG214AriISLbB6B5aw', {
          maxZoom: 18,
          attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/">OpenStreetMap</a> contributors, ' +
              '<a href="https://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>, ' +
              'Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
          id: 'mapbox.streets'
          });
      
      //Creating Google tile layers:
      //Google satellite
      var googleSat = L.tileLayer('http://{s}.google.com/vt/lyrs=s&x={x}&y={y}&z={z}',{
          maxZoom: 20,
          subdomains:['mt0','mt1','mt2','mt3']
          });
      //Google hybrid
      var googleHybrid = L.tileLayer('http://{s}.google.com/vt/lyrs=s,h&x={x}&y={y}&z={z}',{
          maxZoom: 20,
          subdomains:['mt0','mt1','mt2','mt3']
          });
      //Google terrain
      var googleTerrain = L.tileLayer('http://{s}.google.com/vt/lyrs=p&x={x}&y={y}&z={z}',{
          maxZoom: 20,
          subdomains:['mt0','mt1','mt2','mt3']
          });      
                      
      const baseMaps = {
          "OpenStreetMap": osmLayer,
          "SatelliteMap": satellite_tile,
          "MapBox": mapbox_tile,
          "Google satellite": googleSat,
          "Google Hybrid": googleHybrid,
          "Google terrain": googleTerrain
          };
      const layersControl = new L.Control.Layers(baseMaps);
      this.map = new L.Map('lmap', {
              center: this.center,
              zoom: this.zoom,
              layers: [osmLayer]
              });
      const drawnItems = new L.FeatureGroup().addTo(this.map);
      this.map.addControl(layersControl);

      const drawToolBar = new L.Toolbar2.DrawToolbar({
			  position: 'topleft'
      }).addTo(this.map);

      const editToolBar = new L.Toolbar2.EditToolbar.Control({
          position: 'topleft'
      }).addTo(this.map, drawnItems);		

    const appContext = this;
		this.map.on('draw:created', function(evt) {
      appContext.lastEvt = evt;
			const type = evt.layerType,
        layer = evt.layer;
      if(type === 'rectangle'){
        const rect_array = evt.layer._latlngs[0];
				const points = [rect_array[0]['lat'], rect_array[0]['lng'], rect_array[2]['lat'], rect_array[2]['lng']]
				console.log(points);
        evt.layer.on('click', function(e){
          const popup = new L.Popup();
          const lat = +points[2]; //top y coordinate
          const lng = +points[1]; 
          popup.setLatLng([lat, lng+(points[3] - points[1])/2]); //corect popup coodiantes and seting in top center of rectangle
          popup.setContent('Выбран данный прямоугольник. Координаты: ' + points );
          appContext.selectedRectangleLatLng = points;
          appContext.map.openPopup(popup);
        })
			}
			else if(type === 'marker'){
        evt.layer.on('click', function(e){
          const popup = new L.Popup();
          const lat = +evt.layer._latlng['lat'];
          const lng = +evt.layer._latlng['lng'];
          popup.setLatLng([lat+0.011, lng]);
          popup.setContent('Координаты точки ' + ' (' + lat + ', ' + lng + ')');
          appContext.map.openPopup(popup);
        })				
			}
			drawnItems.addLayer(layer);
		});
    });
  }
}
</script>

<style scoped>
#lmap{
  width:100% !important;
  height: 600px !important;
}
.leaflet-toolbar-icon .fa { color: #000; }
.leaflet-color-swatch { background-color: #fff; }
</style>
