<template>
  <div id="container"></div>
</template>

<script>
import bus, {placeInput} from '@/eventBus/eventBus.js'
import AMapLoader from '@amap/amap-jsapi-loader'
window._AMapSecurityConfig = {
    securityJsCode: 'c9ada3067f560745f25900b8cf53c84d' // 安全密钥
}
export default {
  name: 'GdMap',
  data() {
    return {
      // 定义autoOptions和auto
      // auto是用于我们进行输入提示的接收变量，由于是new出的一个对象所以定义初始值为null
      // 而autoOptions是我们要接收的input输入框的id对象所以在其中定义input接收值为空即可
      map: null,
      autoOptions: {
        input: ''
      },
      auto: null,
      placeSearch: null,
      searchInputStr: '',
      district: null,
      polygons: []
    }
  },
  methods: {
    initMap() {
      AMapLoader.load({
        key: '217f742a2872bed13025f46ff0dde325', // 申请好的Web端开发者Key，首次调用 load 时必填
        version: '2.0', // 指定要加载的 JSAPI 的版本，缺省时默认为 1.4.15
        plugins: ['AMap.PlaceSearch','AMap.ToolBar', 'AMap.Scale', 'AMap.HawkEye', 'AMap.MapType', 'AMap.Geolocation', 'AMap.AutoComplete'] // 需要使用的的插件列表，如比例尺'AMap.Scale'等
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

          this.auto = new AMap.AutoComplete(this.autoOptions)
          this.placeSearch = new AMap.PlaceSearch({
            map: this.map
          }) //构造地点查询类
          this.auto.on('select', this.select)
          let marker1 = new AMap.Marker({
            position: new AMap.LngLat(121, 31), // 经纬度对象，也可以是经纬度构成的一维数组[116.39, 39.9]
            title: '上海'
          })
          // 添加点标记
          this.map.add(marker1)
        })
        .catch(e => {
          console.log(e)
        })
    },
    select(e) {
      console.log('mmm',e)
      this.placeSearch.setCity(e.poi.adcode)
      this.placeSearch.search(e.poi.name) //关键字查询查询
      this.drawBounds(e.poi.name)
    },
    // 行政区区域划分
    // 原理: 使用高德地图的AMap.DistrictSearch进行搜索后获取区域边界的坐标值，然后将坐标值提取成数组，最后进行绘制
    drawBounds(newValue) {
      //加载行政区划插件
      if (!this.district) {
        //实例化DistrictSearch
        var opts = {
          subdistrict: 0, //获取边界不需要返回下级行政区
          extensions: 'all', //返回行政区边界坐标组等具体信息
          level: 'district' //查询行政级别为 市
        }

        this.map.plugin(['AMap.DistrictSearch'], () => {
          this.district = new AMap.DistrictSearch(opts)
        })
        // this.district = new AMap.DistrictSearch(opts)
      }
      //行政区查询
      this.district.search(newValue, (status, result) => {
        if (this.polygons != null) {
          this.map.remove(this.polygons) //清除上次结果
          this.polygons = []
        }
        var bounds = result.districtList[0].boundaries
        if (bounds) {
          for (var i = 0, l = bounds.length; i < l; i++) {
            //生成行政区划polygon
            var polygon = new AMap.Polygon({
              strokeWeight: 1,
              path: bounds[i],
              fillOpacity: 0.4,
              fillColor: '#80d8ff',
              strokeColor: '#0091ea'
            })
            this.polygons.push(polygon)
          }
        }
        this.map.add(this.polygons)
        this.map.setFitView(this.polygons) //视口自适应
      })
    }
  },
  created(){
    bus.$on(placeInput, (place)=>{
      this.autoOptions.input = placeInput
      this.searchInputStr = place
    })
  },
  mounted() {
    //DOM初始化完成进行地图初始化
    this.initMap()
  },
  watch: {
    searchInputStr(newValue) {
      if (newValue != null) {
        this.placeSearch.search(newValue)
        this.drawBounds(newValue)
      }
    }
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
