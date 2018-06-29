<template>
  <div class="flex" style="width:100%;height:100%">
      <h4 class="padding">数据查询</h4>
      <div class="padding head">
          <span>
            <span>索引</span>
            <span class="input">
                <select v-model="index">
                    <option v-for="(key,index) in indexData" :key="key" :value="index">{{ index }}</option>
                </select>
            </span>
          </span>
          <span>
              <span>类型</span>
              <span class="input">
                  <select v-model="type">
                    <option v-for="(type,key) in indexData[index]" :key="key" :value="type">{{ type }}</option>
                </select>
              </span>
          </span>
          <button  @click="search">查询</button>
            <button  @click="search">导出</button>
           <span class="padding"> 总数: {{total}}</span>
      </div>
      <div class="table-content">
        <table>
            <tr>
                 <th class="padding tHead" width="100">操作</th>
                <th class="padding tHead" width="100" v-for="(field,key) in fieldsData[type]" :key="key">{{field}}</th>

            </tr>

            <tr v-for="(row,i) in tableData" :key="i">
                <td class="padding tField" width="100">编辑</td>
                <td class="padding tField" width="100" v-for="(field,key) in fieldsData[type]" :key="key">{{row._source[field]}}</td>

            </tr>
        </table>
      </div>
      <div>
          <div class="padding">总数: {{total}}</div>
      </div>

  </div>
</template>
<script>
import get from "../utils/data.js";
export default {
  name: "test",
  data() {
    return {
        index: "zingdinner_v5",
        type: "dinnerClazz",
        indexData: {},    //索引与类型{"index1":["类型1","类型2"]}
        fieldsData:{},  //类型字段{"type1":["字段1","字段2"]}
        tableData:[],
        total:"0",
    };
  },
  created() {
    this.getData();
  },
  methods: {
    getData() {
      let data = get.getData();
      let indexTemp = data.metadata.indices;

      Object.keys(indexTemp).forEach(index => {
        //index 索引
        let indexMappings = indexTemp[index].mappings;
        //类型 [数组]
        this.indexData[index] = Object.keys(indexMappings);
        //字段
        Object.keys(indexMappings).forEach(type => {
            this.fieldsData[type] = Object.keys(indexMappings[type].properties);
        })

      });
    },
    search() {
        let tableData = get.getClazz();
        console.log("111",tableData);
        this.tableData = tableData.hits.hits;
        this.total = tableData.hits.total;

    }
  }
};
</script>
<style scoped>
.padding {
  padding: 5px;
}
.input{
    display: inline-block;
    width: 150px;
}
select{
    width: 100%
}
.flex{
    display: flex;
    flex-direction: column;
}
.table-content{
    width:100%;
    height:100%;
    overflow:auto;
}
.tHead,.tField{
    width: 100px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    border:1px solid #cbcbcb

}
tr:nth-child(odd){
    background:#fafafa;
}
</style>


