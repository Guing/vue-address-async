<template>
  <div id="demo">
    <vue-address-async ref="vSelect" :placeholder="placeholder" :options="options" v-model="value" @change="handleChange" :respone-data="responeData" :respone-data-item="responeDataItem"></vue-address-async>
    <div class="btn-box">
      <button @click="getAddressName" class="btn">获取详细地址</button>
      {{addressName}}
    </div>
  </div>
</template>

<script>
// 引入组件
import VueAddressAsync from "./lib/index";
export default {
  name: "demo",
  components: {
    VueAddressAsync
  },
  data() {
    return {
      //异步请求参数，使用axios插件，具体参数可自行google。
      //(使用的是高德地图的API，访问次数有限，有条件可以使用自己的API)
      options: [
        {
          url: "https://restapi.amap.com/v3/config/district",
          params: { key: "9f05cf737e84708c4cfe5d9f02cddfce" }
        },
        {
          url: "https://restapi.amap.com/v3/config/district",
          params: {
            key: "9f05cf737e84708c4cfe5d9f02cddfce",
            keywords: "$lastValue" //$lastValue可以获取上一个select绑定的值
          }
        },
        {
          url: "https://restapi.amap.com/v3/config/district",
          params: {
            key: "9f05cf737e84708c4cfe5d9f02cddfce",
            keywords: "$lastValue"
          }
        },
        {
          url: "https://restapi.amap.com/v3/config/district",
          params: {
            key: "9f05cf737e84708c4cfe5d9f02cddfce",
            keywords: "$lastValue"
          }
        }
      ],
      //绑定的省市区地址
      value: ["", "", "", ""],
      //select的提示语
      placeholder: ["请选择省", "请选择市", "请选择区", "请选择街道"],
      //获取异步响应数据的格式。
      responeData: "districts[0].districts",
      //异步响应数据中，绑定到option的name与value的key。
      responeDataItem: { name: "name", value: "adcode" },
      //详细地址
      addressName: ""
    };
  },
  methods: {
    /**
     * 获取详细地址
     */
    getAddressName() {
      this.addressName = this.$refs.vSelect.getName();
    },
    /**
     * 改变事件
     */
    handleChange(value, list) {
      console.log(value);
      console.log(list);
    }
  }
};
</script>

<style scoped>
.btn-box {
  margin-top: 20px;
}
</style>


