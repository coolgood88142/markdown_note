## **使用Google Map**取得地址

什麼是Google Map?是由Google公司像全球提供的電子地圖服務，用地圖規劃出地表、線條、符號等資訊。

圖片



### 架構

我們比較常看到的用法，在文字框輸入想要地點或地址之後點查詢，地圖會自動刷新顯示地標以及相關資訊。

流程圖



製作Google Map需要google提供的google map API才能實做，今天我們使用Geocoding API與Map JavaScript API來實做，以下介紹兩種的簡介

### 1. Geocoding API

Geocoding為地理編碼，將地址（如“1600 Amphitheatre Parkway，Mountain View，CA”）轉換為地理坐標（如緯度37.423021和經度-122.083739）的過程，您可以使用它來在地圖上放置標記或定位地圖。



### 2. Map JavaScript API 

通過Maps JavaScript API，您可以使用自己的內容和圖像自定義地圖，以便在網頁和移動設備上顯示。Maps JavaScript API具有四種基本地圖類型（路線圖，衛星，混合和地形），您可以使用圖層和样式，控件和事件以及各種服務和庫來修改這些類型。

#### 

以下範例使用Google Map API的Geocoding API與Map JavaScript API的程式碼


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



6.使用圖文並茂，圖片與文字搭配使得整個文件容易閱讀

7.把實做的部分放進來，附上說明



參考資料:https://zh.wikipedia.org/wiki/Google%E5%9C%B0%E5%9B%BE

https://developers.google.com/maps/documentation/geocoding/intro

https://developers.google.com/maps/documentation/javascript/tutorial