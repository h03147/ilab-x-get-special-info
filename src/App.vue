<template>
  <div id="app">
    <el-row class="elrow_header1">
      <el-col class="elcoldiv1">
        <span class="headerspan1">ilab-x.com 虚拟仿真实验教学共享平台的数据分析</span>
      </el-col>
    </el-row>
    <el-row :gutter="24" class="elrow1" inline="true">
      <el-col :span="12" class="elrow1_col1">
<!--        <el-input v-model="input1" size="small" placeholder="请输入分类编号">-->

<!--        </el-input>-->
        <el-select size="small"
                   class="elselect1"
                   v-model="menuSelect"
                   filterable placeholder="请选择"
                   @change="currStationChange">
          <el-option
                  v-for="item in catalogData"
                  :key="item.value"
                  :label="item.label"
                  :value="item">
            <span style="float: left">{{ item.label }}</span>
            <span style="float: right; color: #48C9B0; font-size: 13px">总数:{{ item.totalNumber }}</span>
            <span style="float: right; color: #FF5733; font-size: 13px; margin-right: 10px;">编号:{{ item.value }}</span>
          </el-option>
        </el-select>
      </el-col>
<!--      <el-col :span="6">-->
<!--        <el-input v-model="input2" size="small" placeholder="请输入起始页">-->

<!--        </el-input>-->
<!--      </el-col>-->
<!--      <el-col :span="6">-->
<!--        <el-input v-model="input3" size="small" placeholder="请输入显示数量">-->

<!--        </el-input>-->
<!--      </el-col>-->
      <el-col :span="2">
        <el-button type="primary" size="small" @click="filterSearch">
            进行查询
        </el-button>
      </el-col>
      <el-col :span="2">
        <el-button type="success"
                   size="small"
                    @click="exportExcel">
          导出excel
        </el-button>
      </el-col>

    </el-row>
    <el-table
            class="eltable1"
            :data="searchTableData">
      <el-table-column
              type="index"
              width="50">
      </el-table-column>
      <el-table-column label="项目学校" prop="schoolTitle"></el-table-column>
      <el-table-column label="项目名称" prop="title"></el-table-column>
      <el-table-column label="项目负责人姓名" prop=".name"></el-table-column>
      <el-table-column label="专业技术职称" prop="techDuty">
        <template slot-scope="scope">
          <el-tag type="success" v-if="scope.row.techDuty == 4">{{techDutyData[4]}}
          </el-tag>
          <el-tag v-else-if="scope.row.techDuty == 3">
            {{techDutyData[3]}}
          </el-tag>
          <el-tag type="info" v-else-if="scope.row.techDuty == 2">
            {{techDutyData[2]}}
          </el-tag>
          <el-tag type="danger" v-else-if="scope.row.techDuty == 1">
            {{techDutyData[1]}}
          </el-tag>
          <el-tag type="warning" v-else-if="scope.row.techDuty == 0">
              {{techDutyData[0]}}
          </el-tag>
            <el-tag type="success" v-else>{{techDutyData[5]}}</el-tag>
        </template>
      </el-table-column>
      <el-table-column label="行政职务" prop="adminDuty"></el-table-column>
      <el-table-column label="所属院系" prop="faculty"></el-table-column>

    </el-table>
  </div>
</template>

<script>
  import Blob from '@/excel/Blob'
  import Export2Excel from '@/excel/Export2Excel.js'
  export default {
  name: 'app',
  data() {
    return {
      searchTableData: [],
      input1: '',
      input2: 1,
      input3: null,
      catalogData: [
        {label: '法学类', value: 217, totalNumber: 15}, {label: '建筑类', value: 227, totalNumber: 21}, {label: '马克思主义理论学', value: 218, totalNumber: 14},
        {label: '体育学类', value: 219, totalNumber: 27}, {label: '历史学类', value: 220, totalNumber: 19}, {label: '物理学类', value: 221, totalNumber: 53},
        {label: '电气类', value: 222, totalNumber: 64}, {label: '矿业类', value: 223, totalNumber: 35}, {label: '兵器类', value: 224, totalNumber: 20},
        {label: '农业工程类', value: 225, totalNumber: 22}, {label: '林业工程类', value: 226, totalNumber: 9}, {label: '自然保护与环境生态类', value: 228, totalNumber: 27},
        {label: '公共卫生与预防医学类', value: 229, totalNumber: 26}, {label: '法医学类', value: 230, totalNumber: 11}, {label: '医学技术类', value: 231, totalNumber: 30},
        {label: '经济管理类', value: 232, totalNumber: 139}, {label: '艺术学类', value: 233, totalNumber: 67}, {label: '生物科学类', value: 0, totalNumber: 73},
        {label: '机械类', value: 1, totalNumber: 173}, {label: '电子信息类', value: 2, totalNumber: 63}, {label: '航空航天类', value: 86, totalNumber: 34},
        {label: '化工与制药类', value: 3, totalNumber: 118}, {label: '交通运输类', value: 4, totalNumber: 60}, {label: '核工程类', value: 5, totalNumber: 22},
        {label: '临床医学类', value: 6, totalNumber: 97}, {label: '药学类', value: 7, totalNumber: 50}, {label: '化学类', value: 80, totalNumber: 119},
        {label: '心理学类', value: 81, totalNumber: 12}, {label: '能源动力类', value: 82, totalNumber: 37}, {label: '土木类', value: 83, totalNumber: 119},
        {label: '测绘类', value: 84, totalNumber: 15}, {label: '环境科学与工程类', value: 87, totalNumber: 30}, {label: '食品科学与工程类', value: 88, totalNumber: 31},
        {label: '植物类', value: 89, totalNumber: 64}, {label: '动物类', value: 90, totalNumber: 56}, {label: '基础医学类', value: 91, totalNumber: 124},
        {label: '中医类', value: 92, totalNumber: 43}, {label: '护理学类', value: 93, totalNumber: 26}, {label: '教育学类', value: 94, totalNumber: 34},
        {label: '地质类', value: 85, totalNumber: 20}, {label: '文学类', value: 95, totalNumber: 60}
      ],
      menuSelect: '',
      techDutyData: ['高级工程师/研究员', '未知', '讲师', '副教授', '教授', '导出格式转换成功'],
    }
  },
  created() {
    const _this = this;
    // https://movie.douban.com/j/search_subjects?type=movie&tag=%E7%83%AD%E9%97%A8&sort=recommend&page_limit=100&page_start=0
    // api: http://www.ilab-x.com/json/projects?status=1&del=0&proLevel=1&isToDeclare=1&limit=12&specialtySubject=232&sortby=pubSeq&reverse=true&ts=1614406963429
    // fetch("http://www.ilab-x.com/json/projects?status=1&del=0&proLevel=1&isToDeclare=1&limit=12&specialtySubject=232&sortby=pubSeq&reverse=true&ts=1614406963429")
    // fetch("/api/j/search_subjects?type=movie&tag=%E7%83%AD%E9%97%A8&sort=recommend&page_limit=100&page_start=0")
    axios
      .get(
              "http://www.ilab-x.com/json/projects?status=1&del=0&proLevel=1&isToDeclare=1&limit=12&specialtySubject=232&sortby=pubSeq&reverse=true&ts=1614406963429"
      )
      .then((response) => {
        const res = response.data;
        // console.log(res);
        // console.log(res.data)
        // _this.searchTableData = res.data;

          // ---------------------------------------------------------
          let list = [];
          for(let i = 0; i < res.data.length; ++i) {
              // console.log('**********' + res.data[i].userInfo.name)
              if(res.data[i].userInfo == null) {
                  let newitem = {
                      schoolTitle: res.data[i].schoolTitle,
                      title: res.data[i].title,
                      name: '空',
                      techDuty: 1,
                      adminDuty: '空',
                      faculty: '空'
                  };
                  list.push(newitem);
              } else {
                  let item = {
                      schoolTitle: res.data[i].schoolTitle,
                      title: res.data[i].title,
                      name: res.data[i].userInfo.name,
                      techDuty: res.data[i].userInfo.techDuty,
                      adminDuty: res.data[i].userInfo.adminDuty,
                      faculty: res.data[i].userInfo.faculty,
                  };
                  list.push(item);
              }
          }
          _this.searchTableData = list;
      });

  },
  methods: {
    // 筛选按钮
    filterSearch() {
      const _this = this;
      axios
      .get(
              "http://www.ilab-x.com/json/projects?status=1&del=0&proLevel=1&isToDeclare=1&" +
              "limit=" + this.input3 +
              "&start=" + this.input2 +
              "&specialtySubject=" + this.input1 +
              "&sortby=pubSeq&reverse=true&ts=1614430400880"
      )
      .then((response) => {
        const res = response.data;
        let list = [];
          for(let i = 0; i < res.data.length; ++i) {
              // console.log('**********' + res.data[i].userInfo.name)
              if(res.data[i].userInfo == null) {
                  let newitem = {
                      schoolTitle: res.data[i].schoolTitle,
                      title: res.data[i].title,
                      name: '空',
                      techDuty: 1,
                      adminDuty: '空',
                      faculty: '空'
                  };
                  list.push(newitem);
              } else {
                  let item = {
                      schoolTitle: res.data[i].schoolTitle,
                      title: res.data[i].title,
                      name: res.data[i].userInfo.name,
                      techDuty: res.data[i].userInfo.techDuty,
                      adminDuty: res.data[i].userInfo.adminDuty,
                      faculty: res.data[i].userInfo.faculty,
                  };
                  list.push(item);
              }

              // let jsonObj = JSON.stringify(res.data[i]);
              // if(jsonObj.indexOf("userInfo.name") <= 0) {
              //     res.data[i].userInfo.name = '未知';
              // } else if(jsonObj.indexOf("userInfo.name") <= 0) {
              //     res.data[i].userInfo.techDuty = '未知';
              // } else if(_this.searchTableData[i].userInfo.adminDuty == null) {
              //     _this.searchTableData[i].userInfo.adminDuty = '未知';
              // } else if(_this.searchTableData[i].userInfo.faculty == null) {
              //     _this.searchTableData[i].userInfo.faculty = '未知';
              // }
          }
        console.log('list***的数据接受' + list);
        _this.searchTableData = list;
        console.log('_this.searchTableData的数据接受');
        console.log(_this.searchTableData);

      });
    },

    // 导出excel方法三 带选中行 带表头样式
    exportExcel() {
      // if (this.selectList.length === 0) {
      //   this.$message({
      //     message: '请至少选择一条需要导出的数据',
      //     type: 'warning'
      //   });
      //   return;
      // }
      this.$confirm("该操作将导出选中的数据，是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
              .then(() => {
                import('@/excel/excelOut').then(excel => {
                  const tHeader = ['项目学校', '项目名称', '项目负责人姓名', '专业技术职称', '行政职务', '所属院系'] //表头
                  const title = ['iLab-x筛选导出的数据', '', '', '', '', '']  //标题
                  //表头对应字段
                  const filterVal = ['schoolTitle', 'title', 'name', 'techDuty', 'adminDuty', 'faculty'];
                    // let list = [];
                    // for(let i = 0; i < this.searchTableData.length; ++i) {
                    //     let item = {
                    //         schoolTitle: this.searchTableData[i].schoolTitle,
                    //         title: this.searchTableData[i].title,
                    //         name: this.searchTableData[i].userInfo.name,
                    //         techDuty: this.searchTableData[i].userInfo.techDuty,
                    //         adminDuty: this.searchTableData[i].userInfo.adminDuty,
                    //         faculty: this.searchTableData[i].userInfo.faculty,
                    //     };
                    //     list.push(item);
                    // }
                  let list = this.searchTableData;
                  this.filterTableData(list)
                    console.log('这是list的数据');
                    console.log(list);
                  let data = this.formatJson(filterVal, list);
                  // let data = this.searchTableData;
                  // data.map(item => {
                  //   // console.log(item)
                  //   item.map((i, index) => {
                  //     if (!i) {
                  //       item[index] = ''
                  //     }
                  //   })
                  // })
                  const merges = ['A1:F1'] //合并单元格
                  excel.export_json_to_excel({
                    title: title,
                    header: tHeader,
                    data,
                    merges,
                    filename: 'ilab-x筛选信息表',
                    autoWidth: true,
                    bookType: 'xlsx',
                    myRowFont: '2'
                  })
                })
              })
              .catch(() => {
                this.$message({
                  type: "info",
                  message: "已取消导出表格操作",
                });
              });
    },
    formatJson(filterVal, jsonData) {//循环重组数据
      return jsonData.map(v => filterVal.map(j => v[j]))
    },
    // 导出数据前对数据处理
    filterTableData(data) {
      data.forEach(item => {
          // item.applyDate = shortTime(item.applyDate)   // 将导出的时间转换成'YYYY-MM-DD'格式
          // item.workAge = item.workAge + '年'
          console.log('这是item数据')
          console.log(item)
          if(item.techDuty == 4) {
              item.techDuty = this.techDutyData[4];
          } else if(item.techDuty == 3) {
              item.techDuty = this.techDutyData[3];
          } else if(item.techDuty == 2) {
              item.techDuty = this.techDutyData[2];
          } else if(item.techDuty == 1) {
              item.techDuty = this.techDutyData[1];
          } else {
              item.techDuty = this.techDutyData[0];
          }
      })
      return data;
      },
    // 下拉选择框的监听
    currStationChange(item) {
      // console.log(item.label);
      // this.$message(item.label + item.value + item.totalNumber);
      this.input1 = item.value;
      this.input3 = item.totalNumber;
      this.$notify({
        title: '选择成功',
        message: '当前选择的分类编号为:' + item.value + '，共有' + item.totalNumber + '条',
        type: 'success',
        position: 'bottom-right'
      });
    },
  }

}
</script>

<style>
  html,body,#app{
    height: 100%!important;
    padding: 0;
    margin: 0;
    overflow-x: hidden;
    overflow-y: auto;
  }

  /* 下拉框elselect1样式*/
  .elselect1 {
    width: 100%;
  }

  /*header里面左边字的样式*/
  .headerspan1 {
    width: 7.6vw;
    height: 1.7vh;
    font-size: 1.8vw;
    font-family: Source Han Sans SC;
    font-weight: 400;
    color: #FFFFFF;
    line-height: 47px;
    margin-left: 1vw;

  }

  /* 标题行的背景颜色*/
  .elrow_header1 {
    width: 100%;
    background-color: #457EDD;
  }

  /* 选择搜索行样式*/
  .elrow1 {
    margin: 1vh;
  }

  /* 下拉选择框*/
  .elrow1_col1 {
    padding-left: 1.2vw !important;
  }
</style>
