---
published: true
category: maps
layout: map
title: "2013/09/20: LLUVIA SEVERA"
splash: "event-0915.png"
basemap: "devseed.fgayuacn"
lat: "22.12"
lng: "-98.05"
zoom: "11"
---



<script type='text/javascript'>

$('#pager').append("<input id='range' class='range' type='range' min='0' max='1.0' step='any' />");

$('.loading').show();
$('#info').hide();
L.mapbox.accessToken = 'pk.eyJ1IjoiZGV2c2VlZCIsImEiOiJnUi1mbkVvIn0.018aLhX0Mb0tdtaT2QNe2Q';
var map = L.mapbox.map('map', null).setView([{{page.lat}}, {{page.lng}}], {{page.zoom}});
var baseLayer = L.mapbox.tileLayer('devseed.tampico2014');
map.addLayer(baseLayer);
var municipalitesLayer = L.geoJson();
var overlay = L.mapbox.tileLayer('devseed.tampicoFlooded').addTo(map);
var range = document.getElementById('range');
var markers = L.mapbox.featureLayer();
var municipalityKey = [];
municipalitesLayer.addTo(map);
markers.addTo(map);


new L.Control.MiniMap(L.mapbox.tileLayer('devseed.jfe5nhb2'), {
        aimingRectOptions: {
            color: '#FFBF00'
        }
    })
    .addTo(map);

 $.ajax({
        type: 'GET',
        url: 'http://201.175.32.249/api/action/datastore_search?resource_id=738a516d-67aa-4b4b-837a-2b81b9c9f61f&filters={"Code": "20300200920130130"}&limit=100000',
        dataType: 'jsonp',
        success: function(data) {
			
			console.log(data);
		
            $.each(data.result.records, function(index, value) {

                if (value.MunId) {

                    var munId = value.MunId.toString();
                    if (munId.length == 4) {
                        munId = '0' + munId;
                    }
                    var stateId = munId.substring(0, 2);
                    var munId = munId.substring(2);
                    
                    
                    var marker = L.marker(new L.LatLng(value['LATITUD'], value['LONGITUD']), {
                            'title': 'test',
                            'id': munId
                        });

                      var markup = '<b>' + value['TIPO.DE.APOYO'] + '</b><br>' + value['MUNICIPIO'] + '<br>' + value['ESTADO'] + '<br>' + withCommas(value['MONTO.RECONSTRUCCION']);


                       // marker.setIcon(L.icon(icon));
                        marker.bindPopup(markup, {
                            autoPan: true
                        });

                        markers.addLayer(marker);
                        
                        $('.loading').hide();
	
		
                }

            });
            
            }
      });
      
      
function withCommas(x) {
    return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
}


function clip() {
  var nw = map.containerPointToLayerPoint([0, 0]),
      se = map.containerPointToLayerPoint(map.getSize()),
      clipX = nw.x + (se.x - nw.x) * range.value;

  overlay.getContainer().style.clip = 'rect(' + [nw.y, clipX, se.y, nw.x].join('px,') + 'px)';
}

range['oninput' in range ? 'oninput' : 'onchange'] = clip;
map.on('move', clip);

clip();


</script>
    