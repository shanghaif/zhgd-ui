<template>
  <div id="l_search">
    <div class="content-box">
      <div class="message">
        <div class="inquire">
          <el-input v-model="name" placeholder="姓名或设备编号或电话"></el-input>
          <button @click="getHireSearch">查询</button>
        </div>
        <div class="message-box">
          <ul>
            <li>
              姓名：
              <span>{{personnelData.userName}}</span>
            </li>
            <li>
              电话：
              <span>{{personnelData.userPhone}}</span>
            </li>
            <li>
              所属公司：
              <span>{{personnelData.constructionName}}</span>
            </li>
            <li>
              设备编号：
              <span>{{personnelData.imei}}</span>
            </li>
            <li>
              设备电量：
              <span v-if="personnelData.bat">{{personnelData.bat}}%</span>
            </li>
            <li>
              定位时间：
              <span>{{personnelData.watchDate}}</span>
            </li>
            <li>
              定位地址：
              <span>{{personnelData.address}}</span>
            </li>
          </ul>
        </div>
      </div>
      <div class="map" v-loading="loading" element-loading-background="rgba(0, 0, 0, 0.8)">
        <el-amap
          ref="map"
          vid="amapDemo"
          :amap-manager="amapManager"
          :center="center"
          :zoom="zoom"
          :plugin="plugin"
          :events="events"
          class="amap-demo"
        >
          <el-amap-polygon
            :path="path"
            strokeColor="#333"
            strokeWeight="2"
            fillColor="#fff"
            fillOpacity="0.5"
          ></el-amap-polygon>
        </el-amap>
      </div>
    </div>
  </div>
</template>

<style lang="less">
#l_search {
  .content-box {
    width: 15rem;
    height: 9.39rem;
    padding: 0.07rem 0.07rem 0.07rem 0.2rem;
    background-image: url("../../../static/images/location-bigBorder.png");
    background-position: center center;
    background-repeat: no-repeat;
    background-size: contain;
    display: flex;
    .message {
      width: 2.55rem;
      .inquire {
        height: 0.72rem;
        transform: translateY(0.18rem);
        .el-input {
          width: 2rem;
          height: 0.5rem;
          padding: 0.01rem;
          .el-input__inner {
            color: #fff;
            height: 0.3rem;
            line-height: 0.2rem;
            font-size: 0.12rem;
            padding-left: 0.07rem;
            border: 0.01rem solid #3375fe;
            border-radius: 0.05rem;
            background-color: #1d1f3b;
          }
          .el-input__prefix {
            display: none;
          }
        }
        button {
          // float: right;
          // margin-right: .18rem;
          font-size: 0.12rem;
          min-width: 0.4rem;
          height: 0.3rem;
          border: 0;
          color: #fff;
          background-color: #00aefe;
          border-radius: 0.05rem;
          transform: translateY(0.41rem);
        }
      }
      .message-box {
        color: #fff;
        font-size: 0.14rem;
        width: 2.5rem;
        transform: translateY(0.4rem);
      }
    }
    .map {
      flex: 1;
    }
  }
}
</style>

<script>
let amapManager = new VueAMap.AMapManager();
export default {
  data() {
    return {
      projectId: sessionStorage.getItem("pid"), // 项目id
      circle: "", // 电子围栏位置信息
      marker: "", // 人员位置坐标点
      name: "", //需要查询的人名或设备编号或手机号码
      createDate: "", //需要查询的定位数据的日期
      polygon: "",
      text: "",
      amapManager,
      zoom: 12,
      center: [114.003378, 22.571492],
      events: {
        init: o => {
          // console.log(o.getCenter())
          // console.log(this.$refs.map.$$getInstance())
          // o.getCity(result => {
          //     console.log(result)
          // })
          let circle1 = new AMap.Circle({
            center: [113.994194, 22.578189],
            fillOpacity: 1, //透明度
            zIndex: 100, //层级
            radius: 10, //半径
            fillColor: "#e10505", //填充颜色
            strokeColor: "#e10505" //轮廓线颜色
          });
          let circle2 = new AMap.Circle({
            center: [114.004537, 22.563725],
            fillOpacity: 1, //透明度
            zIndex: 100, //层级
            radius: 10, //半径
            fillColor: "#e10505", //填充颜色
            strokeColor: "#e10505" //轮廓线颜色
          });
          let circle3 = new AMap.Circle({
            center: [114.009601, 22.570383],
            fillOpacity: 1, //透明度
            zIndex: 100, //层级
            radius: 10, //半径
            fillColor: "#e10505", //填充颜色
            strokeColor: "#e10505" //轮廓线颜色
          });
          let circle4 = new AMap.Circle({
            center: [114.006167, 22.580171],
            fillOpacity: 1, //透明度
            zIndex: 100, //层级
            radius: 10, //半径
            fillColor: "#e10505", //填充颜色
            strokeColor: "#e10505" //轮廓线颜色
          });
          // circle1.setMap(o);
          // circle2.setMap(o);
          // circle3.setMap(o);
          // circle4.setMap(o);
          // marker = [marker1,marker2]
          // marker.setMap(o)
          let polyline = new AMap.Polyline({
            path: [
              new AMap.LngLat(113.994194, 22.578189),
              new AMap.LngLat(114.004537, 22.563725),
              new AMap.LngLat(114.009601, 22.570383),
              new AMap.LngLat(114.006167, 22.580171)
            ],
            lineJoin: "round", //折线拐点样式
            showDir: true, //移动方向
            strokeWeight: 3, //线条宽度
            strokeColor: "#3366ff" //线条颜色
          });
          // polyline.setMap(o);
          let polygon = new AMap.Polygon({
            path: [
              new AMap.LngLat(113.992992, 22.581439),
              new AMap.LngLat(114.01033, 22.582786),
              new AMap.LngLat(114.010287, 22.562853),
              new AMap.LngLat(113.996898, 22.563329)
            ],
            fillColor: "#fff", // 多边形填充颜色
            fillOpacity: 0, //填充颜色透明度
            borderWeight: 1, // 线条宽度
            strokeColor: "#137ed2" // 线条颜色}
          });
          // polygon.setMap(o)

          // 渲染电子围栏
          this.circle = new AMap.Circle({
            center: [114.007675, 22.663599], // 圆心位置
            radius: 0, // 圆半径
            fillColor: "none", // 圆形填充颜色
            fillOpacity: 0, // 填充色透明度
            strokeColor: "#3979fe", // 描边颜色
            strokeWeight: 2 // 描边宽度
          });
          this.circle.setMap(o);

          // 人员位置坐标点
          this.marker = new AMap.Marker({
            position: [114.003378, 22.571492]
          });
          this.marker.setMap(o);
          this.marker.hide();
        },
        moveend: () => {},
        zoomchange: () => {},
        click: e => {
          // alert('map clicked')
        }
      },
      plugin: [
        {
          pName: "ToolBar",
          direction: false,
          events: {
            init(instance) {
              // console.log(instance);
            }
          }
        }
      ],
      getNameState: 0, // getName状态只调用一次
      personnelData: {
        id: 0,
        userName: "",
        userPhone: "",
        constructionName: "",
        imei: "",
        bat: "",
        watchDate: "",
        xloc: "",
        yloc: "",
        address: ""
      }, // 人员数据
      loading: false,
      path: []
    };
  },
  created() {
    this.getName();
  },
  methods: {
    // 获取人员定位数据
    getHireSearch() {
      if (this.name) {
        this.loading = true;
        this.clear(this.personnelData);
        this.$axios
          .post(
            `/api/hireApi/getHireSearch?filed=${this.name}&projectId=${this.projectId}`
          )
          .then(res => {
            this.loading = false;
            if (
              res.data.code == 0 &&
              res.data.data.length &&
              !res.data.data[0].way
            ) {
              this.personnelData = res.data.data[0];
              let temp = res.data.data[0];
              this.circle.setRadius(temp.areaRadius);
              this.circle.setCenter([temp.areaXloc, temp.areaYloc]);
              this.center = [temp.xloc, temp.yloc];
              this.zoom = 14;
              this.marker.setPosition([temp.areaXloc, temp.areaYloc]);
              this.marker.show();
            } else if (
              res.data.code == 0 &&
              res.data.data.length &&
              res.data.data[0].way
            ) {
              this.personnelData = res.data.data[0];
              let temp = res.data.data[0];
              this.path.push(
                temp.localtion.map(a => new Array(a.xloc, a.yloc))
              );
              this.zoom = 14;
              this.center = [temp.xloc, temp.yloc];
              this.marker.setPosition([temp.xloc, temp.yloc]);
              this.marker.show();
            } else {
              this.$message({
                message: "未找到相关人员",
                type: "warning"
              });
            }
          });
        return;
      }
      this.$message({
        message: "输入框不得为空",
        type: "warning"
      });
    },

    // 清除数据
    clear(obj) {
      Object.keys(obj).forEach(key => {
        obj[key] = null;
      });
    },

    // 获取实时监控页面传过来的值
    getName() {
      if (this.$route.query.orderId != undefined && this.getNameState == 0) {
        this.name = this.$route.query.orderId;
        this.getHireSearch();
        this.getNameState = 1;
      }
    }
  }
};
</script>
