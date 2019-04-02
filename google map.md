## **使用Google Map**取得地址

什麼是Google Map?是由Google公司像全球提供的電子地圖服務，用地圖規劃出地表、線條、符號等資訊。



![question](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/question.png>)



### 架構

我們比較常看到的用法，在文字框輸入想要地點或地址之後點查詢，地圖會自動刷新顯示地標以及相關資訊。

以下是我們使用google map的架構圖：



![map](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/map.png>)



製作Google Map需要google提供的google map API才能實做，今天我們使用Geocoding API裡的地理編碼請求與Map JavaScript API來實做，以下介紹兩種的官網文件資訊

### 1. Geocoding API

Geocoding為地理編碼，將地址（如“1600 Amphitheatre Parkway，Mountain View，CA”）轉換為地理坐標（如緯度37.423021和經度-122.083739）的過程，您可以使用它來在地圖上放置標記或定位地圖。

您可以通過HTTP接口訪問Geocoding API。以下是地理編碼請求的示例。

### 地理編碼請求和響應（緯度/經度查找）

以下示例請求“1600 Amphitheatre Parkway，Mountain View，CA”的緯度和經度，並指定輸出必須採用JSON格式。

```html
https://maps.googleapis.com/maps/api/geocode/json?address=1600+Amphitheatre+Parkway,+Mountain+View,+CA&key=YOUR_API_KE
```

您可以通過在Web瀏覽器中輸入URL來進行測試（請務必`YOUR_API_KEY`使用實際的API密鑰替換 ）。響應包括地址的緯度和經度。

以下是JSON中的地理編碼響應示例：

```javascript
{
   "results" : [
      {
         "address_components" : [
            {
               "long_name" : "1600",
               "short_name" : "1600",
               "types" : [ "street_number" ]
            },
            {
               "long_name" : "Amphitheatre Pkwy",
               "short_name" : "Amphitheatre Pkwy",
               "types" : [ "route" ]
            },
            {
               "long_name" : "Mountain View",
               "short_name" : "Mountain View",
               "types" : [ "locality", "political" ]
            },
            {
               "long_name" : "Santa Clara County",
               "short_name" : "Santa Clara County",
               "types" : [ "administrative_area_level_2", "political" ]
            },
            {
               "long_name" : "California",
               "short_name" : "CA",
               "types" : [ "administrative_area_level_1", "political" ]
            },
            {
               "long_name" : "United States",
               "short_name" : "US",
               "types" : [ "country", "political" ]
            },
            {
               "long_name" : "94043",
               "short_name" : "94043",
               "types" : [ "postal_code" ]
            }
         ],
         "formatted_address" : "1600 Amphitheatre Parkway, Mountain View, CA 94043, USA",
         "geometry" : {
            "location" : {
               "lat" : 37.4224764,
               "lng" : -122.0842499
            },
            "location_type" : "ROOFTOP",
            "viewport" : {
               "northeast" : {
                  "lat" : 37.4238253802915,
                  "lng" : -122.0829009197085
               },
               "southwest" : {
                  "lat" : 37.4211274197085,
                  "lng" : -122.0855988802915
               }
            }
         },
         "place_id" : "ChIJ2eUgeAK6j4ARbn5u_wAGqWA",
         "types" : [ "street_address" ]
      }
   ],
   "status" : "OK"
}
```

※要使用地理編碼API，您必須先在Google Cloud Platform控制台中激活API，然後獲取正確的身份驗證憑據。您需要在每個請求中提供**API密鑰**

單擊下面的按鈕以完成您將要執行的過程：

1. 創建或選擇一個項目
2. 啟用API
3. 獲取API密鑰

### 2. Map JavaScript API 

通過Maps JavaScript API，您可以使用自己的內容和圖像自定義地圖，以便在網頁和移動設備上顯示。Maps JavaScript API具有四種基本地圖類型（路線圖，衛星，混合和地形），您可以使用圖層和样式，控件和事件以及各種服務和庫來修改這些類型。

以下網頁顯示以澳大利亞新南威爾士州悉尼為中心的地圖：

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Simple Map</title>
    <meta name="viewport" content="initial-scale=1.0">
    <meta charset="utf-8">
    <style>
      /* Always set the map height explicitly to define the size of the div
       * element that contains the map. */
      #map {
        height: 100%;
      }
      /* Optional: Makes the sample page fill the window. */
      html, body {
        height: 100%;
        margin: 0;
        padding: 0;
      }
    </style>
  </head>
  <body>
    <div id="map"></div>
    <script>
      var map;
      function initMap() {
        map = new google.maps.Map(document.getElementById('map'), {
          center: {lat: -34.397, lng: 150.644},
          zoom: 8
        });
      }
    </script>
    <script src="https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY&callback=initMap"
    async defer></script>
  </body>
</html>
```

使用div名為"map"區塊元素呈現google map，用API的Map物件設定經緯度參數與地圖比例大小，在設定script標記加載Maps JavaScript API 

1. 我們使用聲明將應用程序聲明為HTML5 `<!DOCTYPE html>`。
2. 我們創建了一個`div`名為“map” 的元素來保存地圖。
3. 我們定義了一個JavaScript函數，用於在中創建地圖 `div`。
4. 我們使用`script`標記加載Maps JavaScript API 。

要加載Maps JavaScript API，請使用以下示例中的標記：

```html
<script async defer
  src="https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY&callback=initMap">
</script>
```

`script`標記中包含的URL 是JavaScript文件的位置，該文件加載了使用Maps JavaScript API所需的所有符號和定義。此`script`標記是必需的。

該`async`屬性允許瀏覽器在加載Maps JavaScript API時呈現您網站的其餘部分。API準備就緒後，將調用使用`callback`參數指定的函數。

該`key`參數包含應用程序的API密鑰



以下範例是在開啟地圖與查詢地圖時，顯示的google map的畫面:

### 1.開啟地圖

![open_map1](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/open_map1.png>)

剛開啟地圖時預設是帶台灣地圖，這時可直接輸入地點或地址之後點選查詢，google map會重新讀取地圖

### 2.查詢地點

![open_map2](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/open_map2.png>)

當查詢地點時，google map會重新讀取查詢到的地圖資訊，顯示地點的道路、附近地名等等。

上面顯示剛查詢地點後google map API提供完整地址，可點選右邊的複製圖示，複製地址到文字框做儲存。



以下程式碼是我使用Google Map API的Geocoding API與Map JavaScript API的寫法


	//打開地圖
	function openAddressMap(address,number){
		//經緯度變數
		var Latlng=null;
		
		//判斷查詢的地點或地址確認是否為空值
		if(address!='' && address!=undefined){
			//使用Geocoding API，目前要收費
	        var geocoder = new google.maps.Geocoder(); 
	        //用地點或地址先取得經緯度
	        geocoder.geocode( { 'address': address}, function(results, status) {
	        	//狀態為OK才可以進行
	            if (status == google.maps.GeocoderStatus.OK) {
	                var latitude = results[0].geometry.location.lat(); 
	                var longitude = results[0].geometry.location.lng();
	                
	                //使用Map JavaScript API的LatLng物件當經緯度
	                Latlng = new google.maps.LatLng(latitude,longitude);
	                $("input[name='copy_address']").val(results[0].formatted_address);
	                
	            	//用變數在地圖使用哪些參數
	            	var mapOptions = {
	                	zoom:17,
	                	zoomControl:true,
	                	center:Latlng,
	                	mapTypeId: google.maps.MapTypeId.ROADMAP
	            	}
	            
	            //在頁面上使用Map JavaScript API的Map物件檔地圖(map與copy_map)
	            var map = new google.maps.Map(document.getElementById('map'),mapOptions);
	    		var copy_map = new google.maps.Map(
	                            document.getElementById('copy_map'),
	    						mapOptions);
	    		//使用Map JavaScript API的marker物件設定經緯度 
	            var marker = new google.maps.Marker({
	                position: Latlng
	            });
	            
	            	//id為map與copy_map區塊 set地圖物件
	            	marker.setMap(map);
	            	marker.setMap(copy_map);
	            	
	            	//顯示id為map區塊，隱藏id為copy_map區塊
	                $("#map").show();
	                $("#copy_map").hide();
	                console.log(results[0].formatted_address);
	            } else if (status == google.maps.GeocoderStatus.OVER_QUERY_LIMIT){
	            	//因API有請求計算次數，所以先在背後元素做兩個相同的地圖，當第二次使用可以用另外一张				  顯示達到減少重複相同查詢的次數
	                $("#map").hide();
	                $("#copy_map").show();
	            } else {
	                alert("Geocode was not successful for the following reason: "+status);
	            }
	        });
	    }
	    }
	}
	
	//複製完整地址功能
	function copyAddress(){
	    //先取得地址文字框資料
	    var address = $("input[name='copy_address']").val();
	    if(address==''){
	        alert("請先查詢地點或地址");
	    }else{
	        $("input[name='address']").val(address);
	    }
	}



參考資料:https://zh.wikipedia.org/wiki/Google%E5%9C%B0%E5%9B%BE

https://developers.google.com/maps/documentation/geocoding/intro

https://developers.google.com/maps/documentation/javascript/tutorial