<template>
  <div class='permissiontable'>
    <el-table
      ref='commontable'
      :data="tableData"
      border
      empty-text='No Data'
      @sort-change='sortColumns'
      @selection-change="handleSelectionChange"
      @row-click='clickRow'
      :cell-class-name='changeCellStyle'
      :header-cell-style="rowClass"
      style="width: 100%">
  <!-- 为了防止循环第一项显示在最后一项 -->
      <el-table-column>
        <el-table-column
          width="1"
        >
        </el-table-column>
      </el-table-column>
      <!-- 当设置宽度的时候，循环此div，生成有固定宽度的表格 -->
      <div
        v-for='item in ColumnsData'
        :key='item.id'
        v-if='columnwidth'
      >
        <!--第0列数据-->
        <el-table-column
          width="1"
          v-if="item.id === 0"
        >
          <el-table-column
            width="1"
          >
          </el-table-column>
        </el-table-column>
        <!--1、2列固定的表格-->
        <el-table-column
          :label='item.label'
          align='center'
          fixed='left'
          :width="columnwidth"
          v-else-if="item.id === 1 || item.id === 2"
          sortable='custom'
          show-overflow-tooltip
        >
          <el-table-column
            :width="columnwidth"
            :label='item.label'
            :render-header='addSearch'
            show-overflow-tooltip
          >
            <template slot-scope="scope">
              <p>{{ scope.row[item.prop] }}</p>
            </template>
          </el-table-column>
        </el-table-column>
        <!--除去一些的其他列-->
        <el-table-column
          :label='item.label'
          align='center'
          :width="columnwidth"
          v-else
          sortable='custom'
          show-overflow-tooltip
        >
          <el-table-column
            :width="columnwidth"
            :label='item.label'
            :render-header='addSearch'
            show-overflow-tooltip
          >
            <template slot-scope="scope">
              <p>{{ scope.row[item.prop] }}</p>
            </template>
          </el-table-column>
        </el-table-column>
      </div>
      <!-- 当没设宽度的时使用，用于比较少的列 -->
      <div
        v-for='item in ColumnsData'
        :key='item.id'
        v-if='!columnwidth'
      >
        <el-table-column
          width="1"
          v-if='item.id==0'
        >
          <el-table-column
            width="1"
          >
          </el-table-column>
        </el-table-column>
        <el-table-column
          :label='item.label'
          align='center'
          v-else
          sortable='custom'
          show-overflow-tooltip
        >
          <el-table-column
            :label='item.label'
            :render-header='addSearch'
            show-overflow-tooltip
          >
            <template slot-scope="scope">
              <p>{{ scope.row[item.prop] }}</p>
            </template>
          </el-table-column>
        </el-table-column>
      </div>
      <!--批量修改-->
      <el-table-column align='left' v-if="showcheckbox" width="120px"
      :render-header="renderTableHeader">
        <el-table-column
          type="selection"
          align="left"
          width="120px"
        >
        </el-table-column>
      </el-table-column>
    </el-table>
<!-- 分页 -->
    <el-pagination
      layout="total, sizes, prev, pager, next"
     @size-change="handleSizeChange"
     @current-change='clickPage'
     :page-sizes="[10,100, 500, 1000]"
     :page-size="10"
     :total="total">
    </el-pagination>
  </div>
</template>

<script>
export default {
  name: 'PermissionTable',
  props: {
    showcolumns: {
      type: Boolean,
      default: false
    },
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
    setcolorColmuns: {
      type: Array,
      required: false,
      default: function () {
        return []
      }
    },
    selectedQuarter: {
      type: Array,
      required: false,
      default: function () {
        return []
      }
    },
    selectedWeek: {
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
  data: function () {
    return {
      columnwidth: this.setWidth,
      showcheckbox: this.setCheckbox,
      PmName: this.$store.state.username,
      selectednum: [],
      colorColumns: this.setcolorColmuns,
      ColumnsData: [],
      tableData: [],
      search: {},
      total: 0,
      selectedData: []
      // downloadvalue: []
    }
  },
  watch: {
    setTableTotalData: {
      handler: function (val, oldval) {
        this.ColumnsData = val.columnsData
        this.tableData = val.tableData
        this.search = val.search
        this.total = val.total
      },
      deep: true
    },
    selectedQuarter: {
      handler: function (val, oldval) {
        this.selectedQuarter = val
      }
    },
    selectedWeek: {
      handler: function (val, oldval) {
        this.selectedWeek = val
      }
    }
  },
  mounted () {
  },
  methods: {
    handleSizeChange (val) {
      let numPerPage = val
      this.$emit('getNumPerPage', numPerPage)
    },
    renderTableHeader: function (creatElement, { column, $index }) {
      let _this = this
      return creatElement(
        'el-button',
        {
          class: 'feedback',
          style: {
            width: '95px',
            height: '33px',
            border: '0px solid  #4065A4',
            'background-color': ' #4065A4',
            color: '#fff',
            'border-radius': '20px',
            'font-size': '16px',
            'font-weight': '700',
            'outline': 'none',
            'cursor': 'pointer',
            'padding': '0'
          },
          props: {
            content: 'Feedback',
            trigger: 'click',
            placement: 'top'
          },
          on: {
            click: function () {
              _this.$emit('clickFeedbackButton')
            }
          }
        }, 'Feedback'
      )
    },
    addSearch (h, {column}) {
      let inputValue = {}
      return h('Input', {
        props: {
          placeholder: 'Search' + ' ' + column.label,
          icon: 'ios-search-strong'
        },
        style: {
          paddingRight: '5px'
        },
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
            this.vaildInputValue(column.label, inputValue)
          }
        }
      })
    },
    splicingData () {
      this.selectedData = []
      for (let m of this.selectedQuarter) {
        let quarter = m.slice(0, 2)
        for (let n of this.selectedWeek) {
          if (quarter === n.slice(-2)) {
            this.selectedData.push(m.slice(3).concat(' ', n))
          }
        }
      }
      // 待填入栏位字段
      this.$emit('getSelectedVal', this.selectedData)
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
    clickCheckbox (row, index) {
      if (row.PM !== this.PmName) {
        return 1
      } else {
        return 1
      }
    },
    clickRow (row) {
      if (row) {
        this.$refs.commontable.toggleRowSelection(row)
      } else {
        this.$Message.error('Only modify your own material information')
      }
    },
    rowClass (val) {
      let columnLabel = val.column.label
      if (this.colorColumns.length) {
        for (let v of this.colorColumns) {
          if (v === columnLabel) {
            return 'background:#FFAE06'
          }
        }
      }
    },
    clickPage (page) {
      let pageNum = page
      this.$emit('getPageNum', pageNum)
    },
    changeCellStyle (val) {
      let columnLabel = val.column.label
      let cellVal = val.row.Status
      // 定义状态值的显示不同
      if (columnLabel === 'Status') {
        if (cellVal === 'Good') {
          return 'goodStyle'
        } else if (cellVal === 'Under') {
          return 'underStyle'
        }
        return 'overStyle'
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
.permissiontable {
  .el-checkbox{
    padding-left: 40px;
  }
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
  .el-table td.is-center{
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
  .el-pagination .el-select .el-input .el-input__inner{
    background-color: #313742;
    border: 1px solid #93CEFA;
    color: #D5D8DF;
    padding-right: 35px;
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
  .goodStyle {
    background-color: #9BBD4F;
  }
  .underStyle {
    background-color: #F7A35C;
  }
  .overStyle {
    background-color: red;
  }
  .columnsStyle {
    background-color: #FFAE06!important;
  }
  .columnsStyle div{
    color: #333A46!important;
    font-weight:bold!important;
  }
}
</style>
