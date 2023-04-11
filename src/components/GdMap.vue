<template>
  <div id="container"></div>
</template>

<script>
import AMapLoader from '@amap/amap-jsapi-loader'
window._AMapSecurityConfig = {
    securityJsCode: 'c9ada3067f560745f25900b8cf53c84d' // 安全密钥
}
export default {
  name: 'GdMap',
  data() {
    return {
      map: null
    }
  },
  methods: {
    initMap() {
      AMapLoader.load({
        key: '217f742a2872bed13025f46ff0dde325', // 申请好的Web端开发者Key，首次调用 load 时必填
        version: '2.0', // 指定要加载的 JSAPI 的版本，缺省时默认为 1.4.15
        plugins: ['AMap.ToolBar', 'AMap.Scale', 'AMap.HawkEye', 'AMap.MapType', 'AMap.Geolocation'] // 需要使用的的插件列表，如比例尺'AMap.Scale'等
      })
        .then(AMap => {
          this.map = new AMap.Map('container', {
            //设置地图容器id
            viewMode: '3D', //是否为3D地图模式
            zoom: 10, //初始化地图级别
            center: [121, 31] //初始化地图中心点位置
          })
          this.map.addControl(new AMap.Scale())
          this.map.addControl(new AMap.ToolBar())
          this.map.addControl(new AMap.HawkEye())
          this.map.addControl(new AMap.MapType())
          this.map.addControl(new AMap.Geolocation())
          // let marker1 = new AMap.Marker({
          //   position: new AMap.LngLat(121, 31), // 经纬度对象，也可以是经纬度构成的一维数组[116.39, 39.9]
          //   title: '上海'
          // })
          //添加点标记
          // this.map.marker = [121, 31]
        })
        .catch(e => {
          console.log(e)
        })
    }
  },
  mounted() {
    //DOM初始化完成进行地图初始化
    this.initMap()
  }
}
</script>

<style scoped>
#container {
  padding: 0px;
  margin: 0px;
  width: 100%;
  height: 100%;
}
</style>
