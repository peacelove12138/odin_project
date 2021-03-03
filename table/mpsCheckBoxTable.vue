<template>
  <div class='mpsCheckTable visualList' id="mps">
    <el-table
      :data="tableData"
      border
      empty-text='No Data'
      @sort-change='sortColumns'
      @sort-check='getCurrentRow'
      @selection-change="handleSelectionChange"
      @row-click='clickRow'
      @cell-click='clickCell'
      @cell-dblclick="dbDownFile"
      ref='commontable'
      :cell-class-name='changeCellStyle'
      :cell-style="cellStyle"
      style="width: 100%">
      <!-- 选择框的显示，动态控制，根据checkbox的CheckBox的显示与否 -->
      <el-table-column
        align='center'
        type="selection"
        width="55"
        fixed="left"
      >
      </el-table-column>
      <div
        v-for='item in ColumnsData'
        :key='item.id'
      >
        <el-table-column
          width="1"
          v-if="(item.id==0) || (item.label == 'MPS Name')"
        >
          <el-table-column
            width="1"
          >
          </el-table-column>
        </el-table-column>
        <el-table-column
          :prop="item.prop"
          :label='item.label'
          v-else
          :render-header='addSearch'
          :width="item.id==1 && item.label !== 'PN'? 350: 165"
          :show-overflow-tooltip="item.label=='PN' || item.label=='File name' || item.label=='MPS name'?true:false"
        >
        </el-table-column>
      </div>
      <!-- 动态控制下载按钮的显示 -->
      <el-table-column label='#' align='center' v-if="setDownload">
        <el-table-column width='65;'>
          <template slot-scope='scope'>
            <el-button style="background: #303642" size="mini" @click.native.stop="handleDownload(scope.$index, scope.row)"><img src='../../images/uploadMps.png' alt="" style="width: 23px;"></el-button>
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
  name: 'mpsCheckBoxTable',
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
    setCellLabel: {
      type: String,
      required: false,
      default: ''
    },
    setDownload: {
      type: Boolean,
      required: false,
      default: false
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
    // 必传的四个参数，列的数据，表格数据，搜索数据，总数
    setTableTotalData: {
      type: Object,
      required: true
    }
  },
  data () {
    return {
      valData: [],
      columnwidth: this.setWidth,
      showcheckbox: this.setCheckbox,
      colorColumns: this.setcolorColmuns,
      celllabel: this.setCellLabel,
      showDownload: this.setDownload,
      cellcolor: this.setCellColor,
      ColumnsData: [],
      tableData: [],
      search: {},
      total: 0,
      selectedRowVal: [],
      title: []
    }
  },
  watch: {
    setTableTotalData: {
      handler: function (val, oldval) {
        this.ColumnsData = val.columnsData
        this.tableData = val.tableData
        this.search = val.search
        this.total = val.total
        this.title = val.title
      },
      deep: true
    }
  },
  methods: {
    // 判断多选框选中的值
    getCurrentRow (e, val) {
      var self = this
      self.valData = val.id
      this.$emit('getcheck', self.valData, e)
    },
    // 表格鼠标移入样式
    cellStyle ({row, column, rowIndex, columnIndex}) {
      let cellStyle
      for (let item of this.title) {
        for (let rowItem in row) {
          if (rowItem === item) {
            cellStyle = 'border-left: none;border-right: none'
            // 最后一列显示右边边框
            if (column.label === this.title[this.title.length - 1]) {
              cellStyle = 'border-left: none;'
            }
          }
          // 首列不显示右边边框
          if (columnIndex === 0) {
            cellStyle = 'border-right: none'
          }
          return cellStyle
        }
      }
    },
    addSearch (h, { column, $index }, index) {
      let inputValue = {}
      return h('span', {}, [
        h('span', {slot: 'reference'}, column.label),
        h('el-popover', {props: {placement: 'bottom-start', width: '200', height: '90', trigger: 'hover'}}, [
          h('i', {slot: 'reference', class: 'el-icon-search'}, ''),
          h('span', 'Search', {
            style: 'color: #ffffff;background: #313742;border-radius: 18px;'
          }),
          h('Input', {
            props: {
              placeholder: 'Search' + ' ' + column.label,
              icon: 'ios-search-strong'
            },
            style: 'color: #ffffff;margin-top: 20px;',
            on: {
              input: val => {
                inputValue = val
                if (!inputValue) {
                  this.vaildInputValue(column.label, inputValue)
                }
              },
              class: 'ivu-input-icon',
              'on-click': () => {
                this.vaildInputValue(column.label, inputValue)
              },
              'on-enter': () => {
                this.vaildInputValue(column.label, inputValue, index)
              }
            }
          })
        ])
      ])
    },
    vaildInputValue (label, inputValue) {
      if (!inputValue) {
        this.$set(this.search, label, '')
        this.sendSearchVal()
        return
      }
      if (typeof (inputValue) === 'object') {
      } else {
        this.$set(this.search, label, inputValue)
      }
      this.sendSearchVal()
    },
    sendSearchVal () {
      this.$emit('getSearchVal', this.search)
    },
    sortColumns (val) {
      let label = val.column.label
      let order = val.order
      this.$emit('getSortColumnVal', label, order)
    },
    handleSelectionChange (val) {
      this.selectedRowVal = val
      this.$emit('getSelectedRowVal', this.selectedRowVal)
    },
    clickRow (row) {
      this.$refs.commontable.toggleRowSelection(row)
    },
    clickCell (row, column, cell, event) {
      this.$emit('getCellData', row, column, cell)
    },
    dbDownFile (row, column, cell, event) {
      this.$emit('dbDownFile', row, column, cell)
    },
    clickPage (page) {
      let pageNum = page
      this.$emit('getPageNum', pageNum)
    },
    handleDownload (index, row) {
      this.downloadRowVal = row
      this.showDownload = true
      this.$emit('getDownloadRowVal', this.downloadRowVal)
    },
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
  // 首列定义特殊颜色
  #mps .el-table .cell.el-tooltip{
    color: #2d8df0;
    cursor: pointer;
  }
  .mpsCheckTable {
    .el-checkbox:last-child {
      margin-left: 15px;
    }
    .ivu-input-icon {
      margin-top: 5px;
    }
    .el-table {
      background-color: @background;
      margin-bottom: 5px;
    }
    .el-table .success-row {
      background: #6D778D;
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
