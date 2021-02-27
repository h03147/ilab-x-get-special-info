<template>
  <div id="app">
    <el-row :gutter="20" class="elrow1" inline="true">
      <el-col :span="6">
        <el-input v-model="input1" size="small" placeholder="请输入分类编号">

        </el-input>
      </el-col>
      <el-col :span="6">
        <el-input v-model="input2" size="small" placeholder="请输入起始页">

        </el-input>
      </el-col>
      <el-col :span="6">
        <el-input v-model="input3" size="small" placeholder="请输入显示数量">

        </el-input>
      </el-col>
      <el-col :span="3">
        <el-button type="primary" size="small" @click="filterSearch">
            进行查询
        </el-button>
      </el-col>
      <el-col :span="3">
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
      <el-table-column label="项目学校" prop="schoolTitle"></el-table-column>
      <el-table-column label="项目名称" prop="title"></el-table-column>

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
      input2: null,
      input3: null,
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
        console.log(res);
        console.log(res.data)
        _this.searchTableData = res.data;
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
        _this.searchTableData = res.data;
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
                  const tHeader = ['项目学校', '项目名称'] //表头
                  const title = ['iLab-x筛选导出的数据', '']  //标题
                  //表头对应字段
                  const filterVal = ['schoolTitle', 'title']
                  let list = this.searchTableData;
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
                  const merges = ['A1:B1'] //合并单元格
                  excel.export_json_to_excel({
                    title: title,
                    header: tHeader,
                    data,
                    merges,
                    filename: 'ilab-x筛选表',
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
  }

}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
