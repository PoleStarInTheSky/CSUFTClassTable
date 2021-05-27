<template>
  <div id="app">
    <CustomHeader inputHeader="林大课表" />
    <transition name="el-fade-in-linear" :appear="showAnimation">
    <el-card class="box-card" id="firstCard" >
      <InputInfo @onSearch="(inputGrade,inputMajor,inputClass)=>getClassTable(inputGrade,inputMajor,inputClass)"/>
    </el-card>
    </transition>
    <el-collapse-transition>
   <el-card class="box-card"  v-if="classTableStatus" id="secondCard" >  
   <!--  <el-card class="box-card" id="secondCard">  -->
      <CourseTable :classTable="classTable"/>
    </el-card>
    </el-collapse-transition>
  </div>
</template>

<script>
import CourseTable from './components/CourseTable.vue'
import CustomHeader from './components/CustomHeader.vue'
import InputInfo from './components/InputInfo.vue'
import axios from 'axios'

export default {
  name: 'App',
  data() {
            return {
                showAnimation: true,
                classTable: {},
                
            }
        },
  components: {
    CourseTable,
    CustomHeader,
    InputInfo
  },
  methods: {
    /*
     *用来读取“年级-专业-班级”格式的json课表文件，赋值给data中的classTable对象
     *@param {string} inputGrade - 年级，这里用一个四位数的年份表示，如“2019”。
     *@param {string} inputMajor - 专业全称，要求一字不差完全匹配。
     *@param {string} inputClass - 班级，用一位数的字符串表示。
     */
     getClassTable(inputGrade,inputMajor,inputClass) {
       axios.get('https://ldkb-1257334448.cos.ap-guangzhou.myqcloud.com/json/'+inputGrade+inputMajor+inputClass+'班.json')
       .then(response=>{
          this.classTable=response.data;
          console.log(this.classTable);
       })
       .catch(error=>{
         console.log(error);
         this.alertFun("无此班级，请输入正确的班级信息！");
       });
     },

    /*
     *element-ui提供的组件，用来美化原生alert
     *@param {string} textTittle - alertFun弹窗的标题
     *@param {string} textDetail - alertFun弹窗的内容
     */
    alertFun(textDetail="发生了一个错误！",textTittle="错误") {
        this.$alert(textDetail, textTittle, {
          confirmButtonText: '确定',
        /*
         *点击按钮后的气泡信息，暂时不使用
          callback: action => {
            this.$message({
              type: 'info',
              message: `action: ${ action }`
            });
          }
        */
        });
      },
  

  },
  mounted() {
         //this.getClassTable('2019',"计算机科学与技术",'2')
         //console.log("yes!");
        },
  computed :{
    classTableStatus: function(){
        if (JSON.stringify(this.classTable) === '{}') {
             return false // 如果为空,返回false
          }
              return true
      }

  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.el-scrollbar__wrap {
  overflow-x: hidden;
}
.box-card {
    max-width: 100%;
    margin: 23px;
  }

#secondCard {
  margin-left: 0px;
  margin-right: 0px;
  padding : 0px;
}

/*对element-ui组件的移动端适配*/
@media screen and (max-width: 500px) {
   .el-dialog__wrapper .el-dialog {
      width: 300px !important;
      .el-dialog__body{
        padding: 10px 20px!important;
        .el-form-item__label{
          width: 68px!important;
        }
        .el-select,.el-input{
          width: 180px!important;
        }
      }
    }
}
@media screen and (max-width: 500px) {
    .el-message-box{
      width: 300px !important;
    }
  }

@media screen and (max-width: 500px) {
    .el-message {
      min-width: 300px !important;
    }

@media screen and (max-width: 500px) {
    .el-autocomplete,.el-input {
      width: 155px !important;
    }
  }    
}
</style>
