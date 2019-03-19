**google map**



使用geocoder api 可在頁面元素顯示地圖，從2018年6月11日起google map api要開始進行收費，

在專案因使用想查詢地點或地址時都需要用geocoder api 回傳查詢到的經緯度才可以使用地圖，以及複製功能需要將geocoder api查詢到的完整地址進行複製到文字框無需在輸入，前面要先查詢到地圖，取得查詢地圖的經緯度



目前使用Geocoding API與Map JavaScript API

以下用openAddressMap與copyAddress說明


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
