<template>
  <div class='simpletable'>
    <el-table
      :data="tableData"
      border
      empty-text='No Data'
      @sort-change='sortColumns'
      @selection-change="handleSelectionChange"
      @row-click='clickRow'
      ref='commontable'
      :cell-class-name='changeCellStyle'
      style="width: 100%">

  <!-- 为了防止循环第一项显示在最后一项 -->
      <el-table-column>
        <el-table-column
          width="1"
        >
        </el-table-column>
      </el-table-column>
      <!-- 中间循环列 -->
      <!-- 当设置宽度的时候，循环此div，生成有固定宽度的表格 -->
      <div
       v-for='item in ColumnsData'
       :key='item.id'
       v-if='columnwidth'
      >
          <el-table-column
           v-if="item.label=='id'"
           width="1"
            >
          </el-table-column>
          <el-table-column
           v-else-if='item.id==1||item.id==2'
           :label='item.label'
           :prop="item.prop"
           align='center'
           fixed='left'
           :width="columnwidth"
           sortable='custom'
           show-overflow-tooltip
            >
          </el-table-column>
          <el-table-column
            v-else
            :label='item.label'
            :prop="item.prop"
            align='center'
            :width="columnwidth"
            sortable='custom'
            show-overflow-tooltip
            >
          </el-table-column>
      </div>
      <!-- 当没设宽度的时使用，用于比较少的列 -->
      <div
       v-for='item in ColumnsData'
       :key='item.id'
       v-if='!columnwidth'
      >
        <el-table-column
         v-if="item.label=='id'"
         width='1'
          >
        </el-table-column>
        <el-table-column
         v-else
         :label='item.label'
         :prop="item.prop"
         align='center'
         sortable='custom'
         show-overflow-tooltip
          >
        </el-table-column>
      </div>
<!-- 选择框的显示，动态控制，根据checkbox的CheckBox的显示与否 -->
        <el-table-column
         v-if='showcheckbox'
         label='#'
         type="selection"
         align='center'
          >
        </el-table-column>
      <!-- 动态控制下载按钮的显示 -->
      <el-table-column label='#' align='center' v-if='showDownload'>
        <el-table-column width='110px;'>
          <template slot-scope='scope'>
            <el-button size="mini" @click="handleDownload(scope.$index, scope.row)">DownLoad</el-button>
          </template>
        </el-table-column>
      </el-table-column>
    </el-table>

<!-- 分页 -->
    <el-pagination
     layout="total,prev, pager, next"
     @current-change='clickPage'
     :total="total">
    </el-pagination>
  </div>
</template>

<script>
export default {
  name: 'SimpleTable',
  props: {
    setWidth: {
      type: Number,
      required: false,
      default: 0
    },
    setCheckbox: {
      type: Boolean,
      required: false,
      default: false
    },
    setDownload: {
      type: Boolean,
      required: false,
      default: false
    },
    setCellLabel: {
      type: String,
      required: false,
      default: ''
    },
    setCellColor: {
      type: Array,
      required: false,
      default: function () {
        return []
      }
    },
    setcolorColmuns: {
      type: Array,
      required: false,
      default: function () {
        return []
      }
    },
    // 必传的三个参数，列的数据，表格数据，总数
    setTableTotalData: {
      type: Object,
      required: true
    }
  },
  data () {
    return {
      columnwidth: this.setWidth,
      showcheckbox: this.setCheckbox,
      showDownload: this.setDownload,
      colorColumns: this.setcolorColmuns,
      celllabel: this.setCellLabel,
      cellcolor: this.setCellColor,
      selectednum: [],
      ColumnsData: [],
      tableData: [],
      total: 0,
      selectedRowVal: [],
      downloadRowVal: [],
      changePageCol: [],
      changePageRow: []
    }
  },
  mounted () {
  },
  watch: {
    setTableTotalData: {
      handler: function (val, oldval) {
        this.ColumnsData = val.columnsData
        this.tableData = val.tableData
        this.total = val.total
        console.log(this.ColumnsData, '1111')
      },
      deep: true
    }
  },
  methods: {
    sortColumns (val) {
      let label = val.column.label
      let order = val.order
      this.$emit('getSortColumnVal', label, order)
    },
    handleSelectionChange (val) {
      this.selectedRowVal = val
      this.$emit('getSelectedRowVal', this.selectedRowVal)
    },
    clickRow (row, column) {
      this.$refs.commontable.toggleRowSelection(row)
      this.changePageRow = row
      this.changePageCol = column
      this.$emit('getChangePage', this.changePageRow, this.changePageCol)
    },
    clickPage (page) {
      let pageNum = page
      this.$emit('getPageNum', pageNum)
    },
    handleDownload (index, row) {
      this.downloadRowVal = row
      this.$emit('getDownloadRowVal', this.downloadRowVal)
    },
    // 定义特殊列和cell的特殊色
    changeCellStyle (val) {
      let columnLabel = val.column.label
      let cellVal = val.row[this.celllabel]
      // 定义特殊列的不同值显示不同的颜色
      if (this.celllabel.length && this.cellcolor.length) {
        if (columnLabel === this.celllabel) {
          if (cellVal === this.cellcolor[0]) {
            return 'FirstStyle'
          } else if (cellVal === this.cellcolor[1]) {
            return 'SecondStyle'
          } else if (cellVal === this.cellcolor[2]) {
            return 'ThirdStyle'
          }
          return 'noStyle'
        }
      }
      // 定义哪一列是显示特殊颜色
      if (this.colorColumns.length) {
        for (let v of this.colorColumns) {
          if (v === columnLabel) {
            return 'columnsStyle'
          }
        }
      }
    }
  }
}
</script>
<style lang='less'>
.simpletable {
  .ivu-input-icon {
    margin-top: 5px;
  }
  .el-table {
    background-color: @background;
    margin-bottom: 5px;
  }
  .el-table tr{
    background-color: @background;
    color: #fff;
  }
  .el-table th {
    padding: 2px 0;
  }
  .el-table__body tr.hover-row>td {
      background-color: @domhover;
  }
    .el-table thead.is-group th {
      background: @domhover;
  }
  .el-table--enable-row-hover .el-table__body tr:hover>td {
      background-color: @domhover;
  }
  .el-table--border th {
      border-bottom: 1px solid @bordercolor;
      border-right: 1px solid @bordercolor;
  }
  .el-table td , .el-table th.is-leaf{
    border-bottom: 1px solid @bordercolor;
    border-right: 1px solid @bordercolor;
    text-align: left;
  }
  .el-table--border, .el-table--group {
      border: 1px solid @bordercolor;
  }
  .el-table::before {
    background-color: @bordercolor
  }
  .el-pagination__total {
    color: #fff;
  }
  .el-pagination .btn-next, .el-pager li, .el-pagination .btn-prev{
    background-color: @background;
    color: #fff;
  }
  .el-pagination button:disabled {
    background-color: @background;
  }
  .el-pager li.btn-quicknext, .el-pager li.btn-quickprev {
    color: #fff;
  }
  .el-table th>.cell {
    color: #D5D8DF;
  }
  .el-table .cell {
    color: #D5D8DF;
  }
  .el-pager li {
    background-color: #313742;
    border: 1px solid #93CEFA;
    border-radius: 5px;
    margin: 0 5px;
    color: #D5D8DF;
  }
  .el-pager li.active {
    background-color: #4E76BA;
    border: 1px solid #4E76BA;
    color: #D5D8DF;
  }
  [class*=" el-icon-"], [class^=el-icon-] {
    height: 28px;
    line-height: 28px;
    width: 35px;
    background-color: #313742;
    border: 1px solid #93CEFA;
    border-radius: 5px;
    color: #D5D8DF;
  }
  .el-pagination span:not([class*=suffix]) {
    color: #D5D8DF;
  }
  .ivu-input {
    background-color: #313742;
    border: 1px solid #93CEFA;
    color: #D5D8DF;
  }
  .FirstStyle {
    background-color: #9BBD4F;
  }
  .SecondStyle {
    background-color: #F7A35C;
  }
  .ThirdStyle {
    background-color: red;
  }
  .noStyle {
    background-color: #333A46;
  }
  .columnsStyle {
    background-color: #6D778D;
  }
}
</style>
