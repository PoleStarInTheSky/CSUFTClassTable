<template>

<div> 

<div class="inputChart">
<el-select v-model="inputGrade" placeholder="选择年级">
    <el-option
      v-for="item in options"
      :key="item.value"
      :label="item.label"
      :value="item.value">
    </el-option>
  </el-select>
</div>

<div class="inputChart">

 
  <el-autocomplete
      class="inline-input"
      v-model="inputMajor"
      :fetch-suggestions="querySearch"
      placeholder="输入专业全称"
      :trigger-on-focus="false"
      @select="handleSelect"
    ></el-autocomplete>
  </div>

  <div class="inputChart">
  <el-input
      class="inline-input"
      v-model="inputClass"
      placeholder="输入班级"
    ></el-input>
   

</div>

<div class="inputChart">
  <el-button type="primary" @click="searchBtn">查询</el-button>
</div>

</div>

</template>
 
<script>
    export default {
    name: 'InputInfo',
    data() {
      return {
        majors: [],
        inputMajor:'',
        inputClass: '',
        options: [{
          value: '2020',
          label: '2020'
        }, {
          value: '2019',
          label: '2019'
        }, {
          value: '2018',
          label: '2018'
        }, {
          value: '2017',
          label: '2017'
        }],
        inputGrade: ''
      };
    },
    methods: {
      querySearch(queryString, cb) {
        let majors = this.majors;
        let results = queryString ? majors.filter(this.createFilter(queryString)) : majors;
        // 调用 callback 返回建议列表的数据
        cb(results);
      },
      createFilter(queryString) {
        return (inputMajor) => {
          return (inputMajor.value.toLowerCase().indexOf(queryString.toLowerCase()) === 0);
        };
      },
      loadAll() {
        return [
            { "value": "旅游管理(国际)"},
            { "value": "木材科学与工程"},
            { "value": "工程管理"},
            { "value": "环境工程"},
            { "value": "舞蹈学"},
            { "value": "国际旅游"},
            { "value": "旅游管理"},
            { "value": "国际商务"},
            { "value": "环境生态工程"},
            { "value": "森林保护"},
            { "value": "金融学类"},
            { "value": "电子信息工程(中外合作办学)"},
            { "value": "国际经济与贸易（留学生）"},
            { "value": "物流工程"},
            { "value": "会计学(ACCA卓越班)"},
            { "value": "汽车服务工程"},
            { "value": "林学类"},
            { "value": "软件工程"},
            { "value": "土木类"},
            { "value": "城市地下空间工程"},
            { "value": "市场营销"},
            { "value": "园艺"},
            { "value": "林学(陶铸实验班)"},
            { "value": "建筑学"},
            { "value": "园林"},
            { "value": "朝鲜语"},
            { "value": "市场营销(国际)"},
            { "value": "环境设计"},
            { "value": "地理信息科学"},
            { "value": "材料成型及控制工程"},
            { "value": "金融学(中外合作办学)"},
            { "value": "森林工程"},
            { "value": "土木工程"},
            { "value": "环境科学与工程类"},
            { "value": "林学"},
            { "value": "生物工程"},
            { "value": "会计学"},
            { "value": "国际经济与贸易(国际教育实验班)"},
            { "value": "交通运输"},
            { "value": "生态学"},
            { "value": "人力资源管理"},
            { "value": "旅游信息化技术与管理"},
            { "value": "会计学(中外合作办学)"},
            { "value": "环境科学"},
            { "value": "建筑类"},
            { "value": "机械设计制造及其自动化"},
            { "value": "电子科学与技术"},
            { "value": "酒店管理"},
            { "value": "食品科学与工程类"},
            { "value": "粮食工程"},
            { "value": "日语"},
            { "value": "城乡规划"},
            { "value": "信息与计算科学"},
            { "value": "材料化学"},
            { "value": "保险学"},
            { "value": "物流管理(国际教育实验班)"},
            { "value": "产品设计"},
            { "value": "车辆工程"},
            { "value": "翻译"},
            { "value": "金融学"},
            { "value": "化学工程与工艺"},
            { "value": "新能源科学与工程"},
            { "value": "国际经济与贸易"},
            { "value": "土地资源管理"},
            { "value": "自动化"},
            { "value": "林学(中外合作办学)"},
            { "value": "工业设计"},
            { "value": "水土保持与荒漠化防治"},
            { "value": "风景园林"},
            { "value": "能源与动力工程"},
            { "value": "会展经济与管理"},
            { "value": "旅游管理类"},
            { "value": "测绘工程"},
            { "value": "电子信息工程"},
            { "value": "通信工程"},
            { "value": "食品科学与工程"},
            { "value": "食品质量与安全"},
            { "value": "游憩与公园管理"},
            { "value": "视觉传达设计"},
            { "value": "应用物理学"},
            { "value": "材料科学与工程"},
            { "value": "工程力学"},
            { "value": "法学"},
            { "value": "生物技术"},
            { "value": "会计学(ACCA)"},
            { "value": "林产化工"},
            { "value": "生物科学类"},
            { "value": "俄语"},
            { "value": "农林经济管理"},
            { "value": "金融学(CFA卓越班)"},
            { "value": "法语"},
            { "value": "物流管理"},
            { "value": "计算机科学与技术"},
            { "value": "材料类"},
            { "value": "金融学(CFA)"},
            { "value": "音乐表演"},
            { "value": "社会体育指导与管理"},
            { "value": "行政管理"},
            { "value": "高分子材料与工程"},
            { "value": "英语"}

        ];
      },
      handleSelect(item) {
        console.log(item);
      },

      /*
       *查询按钮点击事件，将用户输入的年级，专业，班级传给主组件App.vue
       */
      searchBtn() {
          this.$emit('onSearch',this.inputGrade,this.inputMajor,this.inputClass);
      }
    },
    mounted() {
      this.majors = this.loadAll();
    }
  }

</script>
 
<style scoped>
  .sub-title {
      color: #C0C0C0;
      font-size: 13px;
  }
  .el-dropdown-link {
    cursor: pointer;
    color: #409EFF;
    font-size: 16px;
  }
  .el-icon-arrow-down {
    font-size: 12px;
  }
  .el-row {
    margin-bottom: 20px;
    max-width:100%;
    &:last-child {
      margin-bottom: 0;
    }  

  }
  .inputChart {
    margin: 9px auto;

  }
  .el-input {
    max-width: 35%;
  }

</style>