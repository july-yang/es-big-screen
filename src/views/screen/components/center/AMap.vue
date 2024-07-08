<template>
	<div id="mapContainer"></div>
</template>

<script setup lang='ts'>
import axios from 'axios'
import { onMounted, shallowRef } from 'vue'
import '@amap/amap-jsapi-types'
import AMapLoader from '@amap/amap-jsapi-loader'
import { dotMarkData } from './amap.js'

const map = shallowRef<AMap.Map | null>(null)

console.log('dotMarkData', dotMarkData)

const communityName = '武汉市高新六路平安光谷春天';
let coordinates = null;

let key = '9eb3bcdeedec1c900183afffcf455c30	'

function getCommunityCoordinates () {
  return axios.get(`https://restapi.amap.com/v3/geocode/geo?key=${key}&address=${communityName}`)
    .then(response => {
      if (response.data.status === '1' && response.data.count > 0) {
        const location = response.data.geocodes[0].location.split(',');
        coordinates = [Number(location[0]), Number(location[1])];
		console.log('coordinates', coordinates)
      } else {
		console.log('无法获取坐标')
        coordinates = '无法获取坐标';
      }
    })
    .catch(error => {
      console.error('获取坐标时发生错误：', error);
    });
};

function initMap() {
	getCommunityCoordinates().then(() => {
		AMapLoader.load({
			key: '9a30a28a90655963b1ad8a483ac8a344', // 申请好的Web端开发者Key，首次调用 load 时必填
			version: '2.0', // 指定要加载的 JSAPI 的版本，缺省时默认为 1.4.15
			plugins: [], // 需要使用的的插件列表，如比例尺'AMap.Scale'等
		}).then((MyAMap: typeof AMap)=>{
			map.value = new MyAMap.Map('mapContainer',{
				zoom: 17.3, //初始化地图层级
				mapStyle: 'amap://styles/dark',
				center: coordinates
			})

			dotMarkData.forEach((data) => {
				const fillValue = data.background
				const redCircleIcon = new MyAMap.Icon({
					size: new MyAMap.Size(10, 10),
					image: 'data:image/svg+xml,' + encodeURIComponent(`<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100"><circle cx="50" cy="50" r="40" fill="${data.background}"/></svg>`),
					imageSize: new MyAMap.Size(10, 10)
				});
	
				// Create a custom text overlay for the marker
				const textOverlay = new MyAMap.Text({
					text: data.title, // Set the text content
					position: data.position, // Text position
					offset: new MyAMap.Pixel(-26, -30), // Offset the text above the marker
					style: {
						'background-color': data.background, // Set background color for text
						'padding': '3px 8px',
						'color': 'white', // Add padding to text
						'font-size': '12px',
						'border': 'none'
					}
				});
	
	
				// Create a marker with the red circle icon and text overlay
				const marker = new MyAMap.Marker({
					position: data.position,
					icon: redCircleIcon
				});
	
				// Add the text overlay to the map
				textOverlay.setMap(map.value);
	
				// Add the marker to the map
				marker.setMap(map.value);

			})
	
		}).catch(e=>{
			console.log(e);
		})
	})
}

function initOfflineMap() {
	var map = new AMap.Map("mapContainer", {
	pitch: 60,
	viewMode: '3D',
	showLabel: true,
	zoom: 17,
	mapStyle: 'amap://styles/whitesmoke',
	center: [114.437876, 30.441431]
	});
}

onMounted(() => {
	initMap()
	// initOfflineMap()
})

defineExpose({
	map
})
</script>

<style lang='scss'>
#mapContainer {
	width: 100%;
	height: 100%;
}
.amap-logo, .amap-copyright {
	opacity: 0;
}
</style>
