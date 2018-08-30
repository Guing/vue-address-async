# vue-address-async

## 简介
vue-address-aync是一个异步的多级地址联动select组件。select元素可以通过异步接口获取数据之后，实现多级联动，因此它不单单可用于多级的地址选择，还可以支持其他的异步多级select联动。  

## 安装  

安装包：  
```bash
# install it via npm
npm install vue-address-async --save
```  
引入组件：  
```javascript
import VueAddressAsync from "vue-address-aync";
//...
  components: {
    VueAddressAsync
  },
//...
```

## Props

**Props**|**类型**|**默认值**|**描述**
-----|-----|-----|-----
options|`Array`|`[]`| 异步请求参数
value|`Array`|`[]`| 绑定的省市区地址
responeData|`String`|`""`| 获取异步响应数据的格式
responeDataItem|`Object`|`{ name: "name", value: "value" }`| 异步响应数据中的key名，用于绑定option的name与value。
placeholder|`Array`|`[]`| select的提示语

### options  
异步请求使用的是axios插件，options具体的请求参数配置，可以查看[官方仓库](https://github.com/axios/axios)。  
由于异步请求可能需要上一个select绑定的值作为请求参数，所以这里添加了一个特殊字符`$lastValue`。  
比如，在Get请求的params参数中，通过`$lastValue`可以获取上一个select绑定的值。  
```javascript
options:[
    {
        url: "https://api.com/province", //获取省的接口
    },
    {
        url: "https://api.com/city",
        params: {
            parentId: "$lastValue" //获取城市，需要传父级省的ID，通过$lastValue获取。
        }
    }
]
```
### value
通过`v-mdoel`可以绑定省市区地址，而且value的元素对应options的接口元素。

### 方法  

### getName
通过调用组件的getName方法，可以获取选中的详细地址名称。

### 事件  

### change
当点击改变select的值，会触发change事件，有两个参数`(value,list)`。  
value：绑定的地址列表。  
list：地址列表数组。  

## 示例  
```html
<template>
  <div id="demo">
    <vue-address-async ref="vSelect" :placeholder="placeholder" :options="options" v-model="value" @change="handleChange" :respone-data="responeData" :respone-data-item="responeDataItem"></vue-address-async>
    <div class="btn-box">
      <button @click="getAddressName" class="btn">获取详细地址</button>
      {{addressName}}
    </div>
  </div>
</template>
```

```javascript
// 引入组件
import VueAddressAsync from "vue-address-aync";
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
```

## 项目构建

``` bash
# 安装依赖
npm install

# 开发环境： localhost:8080
npm run dev

# 构建部署
npm run build
```

