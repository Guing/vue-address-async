<template>
    <div id="vue_address_async">
        <select v-for="(item,index) in cValue" :key="index" v-model="cValue[index]" class="v-select" @change="handleChange(index+1)">
            <option disabled value="">{{placeholder[index]?placeholder[index]:'请选择' }}</option>
            <option v-for="(opt,oIndex) in list[index]" :key="oIndex" :value="opt[responeDataItem.value]">{{opt[responeDataItem.name]}}</option>
        </select>
    </div>
</template>
<script>
import _ from "lodash";
import axios from "axios";
export default {
  name: "VueAddressAsync",
  props: {
    //异步请求参数
    options: {
      type: Array,
      required: true,
      default: []
    },
    //绑定的省市区地址
    value: {
      type: Array,
      required: true,
      default: []
    },
    //获取异步响应数据的格式。
    responeData: {
      type: String,
      default: ""
    },
    //select的提示语
    placeholder: {
      type: Array,
      default: ["请选择省", "请选择市", "请选择区", "请选择街道"]
    },
    //异步响应数据中，绑定到option的name与value的key。
    responeDataItem: {
      type: Object,
      default: { name: "name", value: "value" },
      validator: function(value) {
        if (!value.name || !value.value) {
          return false;
        } else {
          return true;
        }
      }
    }
  },
  data() {
    //根据value的个数创建地址数组
    let list = this.value.map(() => []);
    return {
      list: list //地址列表
    };
  },
  computed: {
    cValue() {
      return this.value;
    }
  },
  created() {
    if (this.options.length > 0) {
      this.setList();
      this.getData(0, false);
    }
  },
  watch: {
    //监控数据value变化，设置相应的地址数组
    value() {
      this.setList();
    }
  },
  methods: {
    /**
     * 改变事件。
     * @param {下标} index
     */
    handleChange(index) {
      if (index >= this.options.length) {
        return false;
      }
      let value = this.cValue.map(
        (item, curIndex) => (curIndex > index - 1 ? "" : item)
      );
      this.$emit("input", value);
      this.getData(index, true).then(() =>
        this.$emit("change", this.value, this.list)
      );
    },
    /**
     * 改变事件。
     * @param {下标} index
     * @param {是否要截掉list[index]后面的元素} cutAfterLength
     */
    getData(index, cutAfterLength) {
      let option = this.options[index];
      let data = this.setParams(option, index);
      return axios(data).then(result => {
        let data = result.data;
        if (!data) {
          return false;
        }
        if (this.responeData != "") {
          let responeData = this.responeData;
          data = new Function("data", "return data." + responeData)(data);
        }
        if (cutAfterLength) {
          let list = this.list.map((item, curIndex) => {
            if (curIndex < index) {
              return item;
            } else if (curIndex == index) {
              return data;
            } else {
              return [];
            }
          });

          this.list = list;
        } else {
          this.list.splice(index, 1, data);
        }
      });
    },
    /**
     * 设置异步请求的参数
     * @param {异步请求参数} option
     * @param {下标} index
     */
    setParams(option, index) {
      let obj = _.cloneDeep(option);
      if (obj && index > 0) {
        obj.data && this.getLastValue(obj.data, index);
        obj.params && this.getLastValue(obj.params, index);
      }
      return obj;
    },
    /**
     * 获取上一个select选中的值
     * @param {异步请求参数} option
     * @param {下标} index
     */
    getLastValue(obj, index) {
      for (let key in obj) {
        if (obj[key] == "$lastValue") {
          obj[key] = this.value[index - 1];
        }
      }
    },
    /**
     * 获取上一个select选中的值
     */
    setList() {
      let self = this;
      this.value.forEach((item, index) => {
        if (index > 0 && item && self.list[index].length == 0) {
          this.getData(index, false);
        }
      });
    },
    /**
     * 获取详细地址
     */
    getName() {
      let names = "";
      let els = document
        .getElementById("vue_address_async")
        .getElementsByTagName("select");
      for (let i = 0, len = els.length; i < len; i++) {
        if (els[i].selectedIndex != 0) {
          names += els[i].options[els[i].selectedIndex].text;
        }
      }
      return names;
    }
  }
};
</script>
<style scoped>
select {
  width: 150px;
  height: 30px;
  font-size: 14px;
  margin-right: 10px;
  border: solid 1px #ccc;
  appearance: none;
  -moz-appearance: none;
  -webkit-appearance: none;
  padding: 2px 14px 2px 10px;
  background: url("../assets/arrow.png") no-repeat scroll right center
    transparent;
}
select::-ms-expand {
  display: none;
}
@media screen and (min-width: 0\0) {
  select {
    background: none\9;
    padding: 5px\9;
  }
}
</style>


