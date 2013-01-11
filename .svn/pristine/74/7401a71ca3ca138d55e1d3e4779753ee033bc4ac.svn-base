    dojo.require("esri.map");
    dojo.require("dijit.layout.BorderContainer");
    dojo.require("dijit.layout.ContentPane");

    var map;
    var mapRef, locuLayer, infoTemplate;

    function init() {
      var initExtent = new esri.geometry.Extent({"xmin":-122.46,"ymin":37.73,"xmax":-122.36,"ymax":37.77,"spatialReference":{"wkid":4326}});
      
      map = new esri.Map("map",{
        extent:esri.geometry.geographicToWebMercator(initExtent)
      });
      //Add the topographic layer to the map. View the ArcGIS Online site for services http://arcgisonline/home/search.html?t=content&f=typekeywords:service    
      var basemap = new esri.layers.ArcGISTiledMapServiceLayer("http://server.arcgisonline.com/ArcGIS/rest/services/World_Topo_Map/MapServer");
      map.addLayer(basemap);
      locuLayer = new esri.layers.GraphicsLayer();
      infoTemplate = new esri.InfoTemplate("${name}", "Adrress: ${street_address}<br />Website: <a>${website_url}</a><br />Phone: ${phone}<br />Menu: ${menus}");
      map.addLayer(locuLayer);
      mapRef = map.spatialReference;
      esri.config.defaults.io.proxyUrl = "http://barndoor/proxy.php";
      dojo.connect(map, 'onLoad', function(theMap) {
        //resize the map when the browser resizes
        dojo.connect(dijit.byId('map'), 'resize', map,map.resize);
      });
    }

    dojo.addOnLoad(init);

    function getLocu() {
      var centerXY = getMapCenter();
      var GeomMapCenter = esri.geometry.webMercatorToGeographic(map.extent.getCenter());
      var locuURL = 'http://api.locu.com/v1_0/venue/search/?api_key=abf7c7d6d8eed2e9703bb5ebb78fe00c45517f3d&has_menu=true&location=' + GeomMapCenter.y + ',' + GeomMapCenter.x + '&radius=5000';
      /*var locuQuery = 'api_key=abf7c7d6d8eed2e9703bb5ebb78fe00c45517f3d&hasmenu=true&bounds=' + map.extent.xmin + ',' + map.extent.ymin + '|' + map.extent.xmax + ',' + map.extent.ymax;*/
      var requestHandle = esri.request({
        url: locuURL,
        load: requestSucceeded,
        error: requestFailed
      });
      //dojo.byId("content").value = "";
      //dojo.removeClass(dojo.byId("content"), "failure");
      //dojo.byId("status").innerHTML = "Downloading...";


      //var url = esri.urlToObject(dojo.byId("url").value);

      //esri.config.defaults.io.proxyUrl = "/proxy/proxy.ashx";


      /*var requestHandle = esri.request({
        url: url.path,
        content: url.query,
        load: requestSucceeded,
        error: requestFailed
      });*/

    }

    function requestSucceeded(response, io) {
      console.log("Succeeded: ", response);
      
      mapLocu(response);

    }

    function requestFailed(error, io) {
      console.log("Failed: ", error);
      dojo.addClass(dojo.byId("content"), "failure");
    }


    function getMapCenter(){
      var MapX = (map.extent.xmax - map.extent.xmin)/2;
      var MapY = (map.extent.ymax - map.extent.ymin)/2;
      var mapCenter = new esri.geometry.Point(MapX, MapY, mapRef);
      return mapCenter;
    }

    function mapLocu(j){
      var marker = new esri.symbol.SimpleMarkerSymbol(esri.symbol.SimpleMarkerSymbol.STYLE_SQUARE, 10,
   new esri.symbol.SimpleLineSymbol(esri.symbol.SimpleLineSymbol.STYLE_SOLID,
   new dojo.Color([255,0,0]), 1),
   new dojo.Color([0,255,0,0.25]));
      //new esri.symbol.SimpleMarkerSymbol().setSize(4);
      var k = j.objects;
      dojo.forEach(k, dojo.hitch(this, function(object){
        var lt = object.lat;
        var ln = object.long;
        var geoPointLL = new esri.geometry.Point(parseFloat(ln), parseFloat(lt), mapRef);
        var geoPointWM = esri.geometry.geographicToWebMercator(geoPointLL);
        var graphic = new esri.Graphic(geoPointWM, marker);
        graphic.setAttributes(object);
        graphic.setInfoTemplate(infoTemplate);
        locuLayer.add(graphic);
        var lLayer = locuLayer;
      }));
      dojo.hitch
    }
    