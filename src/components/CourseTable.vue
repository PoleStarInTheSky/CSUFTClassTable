<!-- 在setTime()函数中，把开学日期填成了2020-09-14 -->
<template>
    <div class="class-table"> 
    <div class="head"> 
    <div>{{classTable.classname}} </div>
    <div class="select-row">
    <i class="left" @click="changeWek('previous')"  ></i>
    <span style="font-weight: normal">{{'第'+ selectWeek +'周'}}</span>
    <i class="right" @click="changeWek('next')"   ></i>
    </div>
    </div> 
    <div class="table-wrapper">  
    <div class="tabel-container">  
    <table>  
        <thead>  
            <tr> 
                <th><div id="plus" @click="addVisible=true">＋</div></th>
                <th v-for="(item,index) in weeks" :class="('周'+'日一二三四五六'.charAt(WekDayNow)==item)?'thisDay':''" >
                <div>{{item}}</div>
                <div>{{ tableGetDate(index)}}</div>
                </th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="(itemSec,indexSec) in sections" >
                <td style="font-size:12px;background:#d4f7fd">
                <p>{{ itemSec.courseIndex }}</p>
                <p>{{ itemSec.courseBegin }}</p>
                <p>{{ itemSec.courseEnd }}</p>
                </td>
                <!-- 课表主体,真正显示课程信息那一部分 -->
                <td v-for="(itemWek,indexWek) in weeks" >
                    <div v-if="haveClass(itemWek,indexSec+1)" :style="{backgroundColor:colorArrays[(getMpKeySym(itemWek,indexSec+1))%9]}" @click="toDetail(itemWek,indexSec+1)">
                    <!-- indexSec+1 因为要和json文件中的section相对应 -->
                     {{ getTableInfo(itemWek,indexSec+1) }}
                    </div>
                </td>
                <!-- 课表主体 -->
            </tr>
        </tbody>
    </table>  
    </div>  
    </div>

    <!-- 详情，修改框 -->
     <el-dialog title="课程详情" :visible.sync="dialogDetailVisible">
        <!-- 详情框中展示内容的部分 -->
            <div class="dialogDiv" v-if="selectCourse.class_room">教室:  {{selectCourse.class_room}}</div>
            <div class="dialogDiv">课程名称:  {{selectCourse.course_name}}</div>
            <div class="dialogDiv">老师:  {{selectCourse.teacher}}</div>
            <div class="dialogDiv">上课周数: {{selectCourse.week}}</div>
            <div class="dialogDiv">上课时间: 第{{selectCourse.section}}大节</div>
        <!-- 详情框中展示内容的部分 -->


        <!-- 编辑框 -->
            <el-dialog
            width="40%"
            title="编辑课程"
            :visible.sync="editVisible"
            append-to-body>
            <div class="dialogDiv" v-if="selectCourse.class_room">
               <b>教室</b>:  <el-input v-model="selectCourse.class_room" placeholder="请输入内容"></el-input>
            </div>
            <div class="dialogDiv">
                <b>课程名称</b>:  <el-input v-model="selectCourse.course_name" placeholder="请输入内容"></el-input>
            </div>
            <div class="dialogDiv">
                <b>老师</b>:  <el-input v-model="selectCourse.teacher" placeholder="请输入内容"></el-input>
            </div>
            <div class="dialogDiv">
                <!-- 正则只允许输入数字'-'和'周'字以及',' '，'-->
                <b>上课周数</b>: <el-input v-model="selectCourse.week" placeholder="请输入内容" oninput="value=value.replace(/[^\d-\u5468,，]/g,'')"></el-input>
            </div>
            <div class="dialogDiv">
            <b>上课时间段</b>:
            <el-input v-model="selectCourse.section" placeholder="请输入内容" oninput="value=value.replace(/[^123456]/g,'')"></el-input>
            </div>
            <div class="dialogDiv"><b>上课星期</b>:
            <el-select v-model="addDay" placeholder="选择星期">
                <el-option
                v-for="item in weeks"
                :key="item"
                :label="item"
                :value="item">
                </el-option>
            </el-select>
            </div>
            <div slot="footer" class="dialog-footer">
                <el-button type="primary" @click="addCourseBtn(selectCourse);deleteCourse(false);editVisible = false;dialogDetailVisible=false">确 认</el-button>
            </div>
            </el-dialog>
        <!-- 编辑框 -->

    <div slot="footer" class="dialog-footer">
      <el-button @click="deleteCourse(true);dialogDetailVisible=false">删 除</el-button>
      <el-button type="primary" @click="editVisible = true">编 辑</el-button>
    </div>
  </el-dialog>
    <!-- 详情，修改框 -->

    <!--增加框-->
    <el-dialog title="增加课程"  :visible.sync="addVisible">
            <div class="dialogDiv" >
               <b>教室</b>:  <el-input v-model="addCourse.class_room" placeholder="请输入内容"></el-input>
            </div>
            <div class="dialogDiv">
                <b>课程名称</b>:  <el-input v-model="addCourse.course_name" placeholder="请输入内容"></el-input>
            </div>
            <div class="dialogDiv">
                <b>老师</b>:  <el-input v-model="addCourse.teacher" placeholder="请输入内容"></el-input>
            </div>
            <div class="dialogDiv">
                <!-- 正则只允许输入数字'-'和'周'字以及',' '，'-->
                <b>上课周数</b>: <el-input v-model="addCourse.week" placeholder="请输入内容" oninput="value=value.replace(/[^\d-\u5468,，]/g,'')"></el-input>
            </div>
            <div class="dialogDiv">
            <b>上课星期</b>:
            <el-select v-model="addDay" placeholder="选择星期">
                <el-option
                v-for="item in weeks"
                :key="item"
                :label="item"
                :value="item">
                </el-option>
            </el-select>
            </div>

            <div class="dialogDiv">
            <b>上课时间段</b>:
            <el-select v-model="addCourse.section" placeholder="选择时间段">
                <el-option
                v-for="(item,index) in sections"
                :key="item.courseIndex"
                :label="item.courseIndex"
                :value="index+1">
                </el-option>
            </el-select>
            </div>
            <div slot="footer" class="dialog-footer">
                <el-button type="primary" @click="addCourseBtn(addCourse);addVisible=false">确 认</el-button>
                <el-button @click="resetAddCourse()">清空</el-button>
            </div>
            </el-dialog>

    <!--增加框-->
    </div> 
</template>
 
<script>
    export default {
        name: "CourseTable",
        data(){
            return {
                weeks:[
                    "周一",  
                    "周二",  
                    "周三",  
                    "周四",  
                    "周五",  
                    "周六",  
                    "周日",
                ],
                sections:[
                    {"courseIndex":"1-2节" ,"courseBegin" :"08:00","courseEnd":"09:40"},
                    {"courseIndex":"3-4节" ,"courseBegin" :"09:55","courseEnd":"11:35"},
                    {"courseIndex":"5-6节" ,"courseBegin" :"14:00","courseEnd":"15:40"},
                    {"courseIndex":"7-8节" ,"courseBegin" :"15:55","courseEnd":"17:35"},
                    {"courseIndex":"9-10节" ,"courseBegin" :"19:00","courseEnd":"20:40"},
                    {"courseIndex":"11-12节" ,"courseBegin" :"20:55","courseEnd":"22:35"}
                ],
                cteMap:new Map(),//cteMap = Chinese to English Map 星期名称的映射,
                selectWeek:12,//选中的周数
                WekTable:new Map(),//WekTable是一个从字符串到对象的映射
                colorArrays: ['#ef5b9c','#f15b6c', '#f26522', '#8552a1', '#7fb80e', '#65c294', '#78cdd1', '#33a3dc','#a5d16d'],
                WekDayNow:"",//今天是周几
                selectMonDay:new Date(),//本周一的时间对象，便于在课表中显示哪天是几号
                thisWeek:1, //本周是第几周
                dialogDetailVisible:false, //详情框是否可见
                editVisible:false,//编辑框是否可见
                addVisible:false,//增加框是否可见
                selectCourse:{},//被选中的课程对象
                selectKey:"",//被选中课程对象的key值
                addCourse:{},//用来加入的课程对象
                addDay:""//判断要加入的日期是哪一天

            }
        },

        props:{
            //从json文件中读取的课程表对象
            classTable:{
                type:Object,
                default:{}
            }
            
        },


        methods:{
                /*
                 *课表中的星期是中文，json中的星期是英文，
                 *用此函数建立中文到英文的映射，如 星期一 -> Monday
                 *此映射在mounted时调用，且只需要建立一次
                 */
                WekChnToEng(cteMap) {
                    cteMap.set("周一","Monday");
                    cteMap.set("周二","Tuesday");
                    cteMap.set("周三","Wednesday");
                    cteMap.set("周四","Thursday");
                    cteMap.set("周五","Friday");
                    cteMap.set("周六","Saturday");
                    cteMap.set("周日","Sunday");
                },

                /*
                 *用来判断某一节是否有课
                 *由此判断课表中某一个格子是否该被渲染
                 *@param {String} itemWek - “周X”格式的字符串，如“周一”
                 *@param {Number} indexSec - 注意这里比传进来的indexSec数值要大一，保证是真实的节数
                 *@return {Boolean} - 是否有课
                 */
                haveClass(itemWek,indexSec){
                    let strKey = itemWek + '第' + indexSec + '节';
                    if(this.WekTable.has(strKey)){
                        return true;
                    }else{
                        return false;
                    }
                },

                /*
                 *返回某一节课的课程信息
                 *由此判断课表中某一个格子是否该被渲染
                 *@param {String} itemWek - “周X”格式的字符串，如“周一”
                 *@param {Number} indexSec - 注意这里比传进来的indexSec数值要大一，保证是真实的节数
                 *@return {String} - 课程表信息，如“面向对象与Java程序设计@树人楼(一教)北405”
                 */
                getTableInfo(itemWek,indexSec){
                    let strKey = itemWek + '第' + indexSec + '节';
                    let classObj = this.WekTable.get(strKey);
                    let tableInfo = classObj.course_name;
                    //有些课没有教室，比如体育课
                    if(classObj.class_room!=''){
                        tableInfo +='@' + classObj.class_room ;
                    }
                    return tableInfo;
                },

                /*
                 *生成本周课表到this.WekTable中,这是一个Map对象
                 *@param {Object} classTable - 课表对象。
                 */
                generateClassTable(classTable){
                    //清空当前WekTable
                    this.WekTable.clear();
                    //第一层，对classTable中的Monday,TuesDay等逐个遍历
                    for (let valChn of this.weeks){
                        //把中文映射为英文，从而取得json对象中的星期数组
                        let val = this.cteMap.get(valChn);
                        //console.log(val);
                        //第二层，对Monday,TuesDay等的内部的课程元素进行遍历
                        for(let valClass of classTable[val]){
                            //第三层，对最里面的week_array数组遍历，看本周有没有开设这门课
                            for(let valOpenWeek of valClass.week_array ){
                                //本周开设了这门课，加入WekTable中
                                if(valOpenWeek===this.selectWeek.toString()){
                                    //生成键，键的格式为“周X第X节” ， 如 “周一第1节”
                                    let strKey = valChn + '第'+valClass.section + '节';
                                    this.WekTable.set(strKey,valClass)
                                }
                            }
                        }
                        
                    }
                    console.log("valid class this week is:");
                    console.log(this.WekTable);
                },

                /*
                 *切换周次按钮的点击事件
                 *@param {String} criterion - 由此判断周数的加减
                 */
                changeWek(criterion){
                    if(criterion=="previous"){
                        if(this.selectWeek==1){
                            this.selectWeek=24;
                            this.selectMonDay.setDate(this.selectMonDay.getDate()+23*7);
                        }else{
                            this.selectWeek--;
                            this.selectMonDay.setDate(this.selectMonDay.getDate()-7);
                        }
                        
                    }
                    else if(criterion=="next"){
                        if(this.selectWeek==24){
                            this.selectWeek=1;
                            //日期直接回退24周
                            this.selectMonDay.setDate(this.selectMonDay.getDate()-23*7);
                        }else{
                            this.selectWeek++;
                            this.selectMonDay.setDate(this.selectMonDay.getDate()+7);
                        }
                        
                    }
                },
                /*
                 *对今天的一些时间变量初始化
                 */
                setTime(){
                    //今天的时间对象
                    let date = new Date();

                    //初始化今天是周几
                    this.WekDayNow= date.getDay();

                    //初始化今天是开学第几周,这里设置为2020-09-14
                    let dateStart = new Date(2021,2,1);
                    //用这种方式生成，保证都是从0点开始的日期
                    let dateEnd = new Date(date.getFullYear(),date.getMonth(),date.getDate());
                    //从开学到现在过了几周，从开学到现在过了几天
                    let gapDays = (dateEnd-dateStart)/(1000*60*60*24);
                    let gapWeek = gapDays/7 +1;
                    //把选择的周数先定位本周
                    this.selectWeek = parseInt(gapWeek);
                    this.thisWeek = parseInt(gapWeek);

                    //今天相对于周一偏移了几天，便于初始化周一的时间对象
                    let offSetMonday =  parseInt( ('6012345'.charAt(this.WekDayNow)) );
                    this.selectMonDay.setDate(this.selectMonDay.getDate()-offSetMonday);

                },
                /*
                 *用于得到表头中显示的日期
                 *@param {Number} offset - 距离周一的偏差值
                 *@return {String} - "xx-xx"格式的日期，如"11-2"
                 */
                tableGetDate(offset){
                    //将this.selectMonDay保护起来，所以重新生成一个对象
                    let copyDate = new Date(this.selectMonDay);

                    copyDate.setDate(copyDate.getDate()+offset);
                    let strDate = (copyDate.getMonth()+1) + '-' + copyDate.getDate();
                    return strDate; 
                },
                /*
                 *课表每一项的点击事件，点击后弹出dialog,并将selecCourse指定为被点击的课程
                 *@param {String} itemWek - “周X”格式的字符串，如“周一”
                 *@param {Number} indexSec - 注意这里比传进来的indexSec数值要大一，保证是真实的节数
                 */
                toDetail(itemWek,indexSec){
                    this.selectKey = itemWek + '第' + indexSec + '节';
                    this.selectCourse = this.WekTable.get(this.selectKey);
                    this.dialogDetailVisible=true;
                    this.addDay=itemWek;
                },
                /*
                 *删除按钮点击事件，点击后将把this.selectCourse课程删除
                 *@param {Boolean} detail - 是否显示警告信息
                 */
                deleteCourse(detail){
                    if(detail){
                        this.$confirm('确认要删除本课?', '提示', {
                            confirmButtonText: '确定',
                            cancelButtonText: '取消',
                            type: 'warning'
                            }).then(() => {
                            //可达性！只要可达就会留下！所以必须设为空
                            let nowWekDay = this.cteMap.get(this.selectKey.slice(0,2));
                            //console.log(nowWekDay);
                           this.classTable[nowWekDay]=
                           this.classTable[nowWekDay].filter(val =>{
                                   return val != this.selectCourse ;                  
                            } );
                            console.log(this.selectKey);
                            this.WekTable.delete(this.selectKey);
                            //使被删除的对象不可达，去除指向他的所有应用，最后被删除
                            this.selectKey='';
                            this.selectCourse=[];
                            this.$message({
                                type: 'success',
                                message: '删除成功!'
                            });
                            }).catch(() => {
                            this.$message({
                                type: 'info',
                                message: '删除失败'
                            });          
                            });
                    }else{
                        let nowWekDay = this.cteMap.get(this.selectKey.slice(0,2));
                            //console.log(nowWekDay);
                            //找到数组中的那门课，删除
                           this.classTable[nowWekDay]=
                           this.classTable[nowWekDay].filter(val =>{
                                   return val != this.selectCourse ;                  
                            } );
                            console.log(this.selectKey);
                            this.WekTable.delete(this.selectKey);
                            //使被删除的对象不可达，最后被删除
                            this.selectKey='';
                            this.selectCourse=[];
                    }
                    
                },
                /*
                 *将用来传值的对象初始化
                 *addCourse是增加框生成的对象
                 */
                resetAddCourse(){
                    this.addCourse = new Object({
                    class_room:"",
                    course_name:"",
                    section:"",
                    teacher:"",
                    week: "",
                    week_array:[]
                });
                },
                /*
                 *增加课程按钮的点击事件
                 *@param {Object} originCourse - 要用来增加课程的原来对象 
                 */
                addCourseBtn(originCourse){
                    //由表单中的周几得到相对应的英文
                    let engAddDay = this.cteMap.get(this.addDay);
                    this.addDay="";
                    //创建一个新的对象并深拷贝来作为插入的元素,避免互相影响
                    //copyCourse是this.addCourse的深拷贝
                    let copyCourse = {};
                    copyCourse=JSON.parse(JSON.stringify(originCourse));
                    //重新生成week_array属性
                    this.parseInputWeeks(copyCourse);                 
                    //加入对象数组，完成
                    this.classTable[engAddDay].push(copyCourse);
                    //originCourse = {};
                    this.resetAddCourse();
                },

                /*
                 *对输入的周数进行解析的函数
                 *具体来说，就是根据课程信息对象重新输入的week属性重新生成他的week_arry属性
                 *@param {Object} copyCourse - 一个课程信息的对象
                 */
                 parseInputWeeks(copyCourse){
                    //先将对象的week_arry清零，因为后面要重新生成week_array
                    copyCourse.week_array=[];
                    //先把汉字“周”剔除，再根据中文逗号和英文逗号由课程生成一个分割数组
                    let weekList=copyCourse.week.replace('周','');
                    weekList = weekList.split( /[,，]+/g );

                    //对分割数组中的元素进行遍历
                    for(let val of weekList){
                        if(val.search( /\d+-\d+/ ) != -1){
                            //当分割数组中的元素是一个时间段时的操作，如“1-2”
                            let handleArr = val.split('-');
                            for(let i = parseInt(handleArr[0]);i<=parseInt(handleArr[1]);i++){
                                copyCourse.week_array.push(i.toString());
                            }
                        }else{
                            //当分割数组中的元素是一个数时的操作，如“1”
                            copyCourse.week_array.push(val.toString());
                        }
                    }
                 },
                 /*
                  *由map的key值的unicod码生成一个数值，保证相同的key值生成相同的数值
                  *这里的主要用法是让课表中，课程名字相同的课程背景颜色相同
                  *@param {String} itemWek - “周X”格式的字符串，如“周一”
                  *@param {Number} indexSec - 注意这里比传进来的indexSec数值要大一，保证是真实的节数
                  *@return {Number} - 返回一个数值，由key确定且是固定的
                  */
                  getMpKeySym(itemWek,indexSec){
                        let strKey  = this.WekTable.get(itemWek + '第' + indexSec + '节').course_name;       
                        let sum = 0;
                        for(let i=0;i<strKey.length;i++){
                            sum += strKey.charCodeAt(i);
                        }
                        return sum;
                  }
                

        },
        mounted() {
            this.WekChnToEng(this.cteMap);
            this.setTime();
            this.resetAddCourse();
        },
        watch:{
        //对课表对象监听
        classTable:{
            handler(val, oldVal){
                    console.log("classTable has changed");
                    console.log(val);
                    //每当calssTable有变化就重新生成本周课表
                    //可能的变化如:增删课程，重新提交查询表单等
                    this.generateClassTable(val);
                    //重新渲染本组件
                    this.$forceUpdate();
                },
            deep: true

        },
        //对当前周数监听
        selectWeek(val,oldVal){
            console.log("selectWeek has changed");
            console.log(val);
            if (JSON.stringify(this.classTable) != '{}') {
             this.generateClassTable(this.classTable);
             this.$forceUpdate();
            }           
        }
        }
    }
</script>
 
<style scoped>
   * {  
    margin: 0;  
    padding: 0;  
    }  
    .table-wrapper {  
    width: 100%;  
    overflow: auto;  
    margin-bottom: 60px;  
    }  
    table {  
    table-layout: fixed;  
    width: 100%;  
    border-collapse: collapse;  
    color: #677998;  
    }  
    
    thead {  
    background: #d4f7fd;  
    }  
    th {  
    font-size: 15px;
    font-weight: normal;  
    height: 46px !important;  
    }  
    tbody {  
    font-size: 12px;  
    }  
    th,  
    td {  
    text-align: center;  
    height: 80px;  
    border-right: 1px solid #fefefe;  
    border-bottom: 1px solid #fefefe;  
    }  
    tr td div {  
    width: 100%;  
    height: 100%;  
    color: #efefef;  
    border-radius: 10px;  
    padding: 5px;  
    box-sizing: border-box;  
    font-size: 11.6px;
    word-break: break-all;
    word-wrap: break-word; 
    }  
    
    tr td:first-child {  
    color: #333;  
    }  
    .course {  
    background-color: #ebbbbb;  
    color: #fff;  
    display: inline-block;  
    border-radius: 3px;  
    width: 47%;  
    margin: 1px 1%;  
    }  
    .bgColor {  
    background-color: #89b2e5;  
    }  
    tr td p {  
        width:100%;
        margin:1.2px 0px;
    }
    .head {
        background-color:#D4F7FD;
        padding: 6px;
        font-size: 16px;
        font-weight:bold;
    }

    i {
        border: solid black;
        border-width: 0 3px 3px 0;
        display: inline-block;
        padding: 3px;
        width:5.6px;
        height:5.6px;
        }

        .right {
            transform: rotate(-45deg);
            -webkit-transform: rotate(-45deg);
            margin-left:40px;
        }

        .left {
            transform: rotate(135deg);
            -webkit-transform: rotate(135deg);
            margin-right:40px;
        }
        .select-row {
            margin-top:13px;
        }
        .thisDay {
            font-weight:bold;
            border:solid;
        }
        .el-button {
            padding: 5.4px;
        }
        .dialogDiv{
            margin: 6px;
        }
        #plus {
            font-weight:900;
            font-size :35px;
            color:black;
        }
        .el-card__body {
        padding: 3px;
        }
     
</style>