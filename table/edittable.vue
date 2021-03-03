<template>
  <div class='edittable'>
    <el-table
      :data="tableData"
      border
      empty-text='No Data'
      @sort-change='sortColumns'
      ref='commontable'
      style="width: 100%">

      <!-- 为了防止循环第一项显示在最后一项 -->
      <el-table-column>
        <el-table-column width="1">
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
          :class-name='item.label'
          align='center'
          fixed='left'
          :width="columnwidth"
          v-else-if='item.id==1||item.id==2'
          sortable='custom'
          show-overflow-tooltip
        >
          <el-table-column
            :prop="item.prop"
            :width="columnwidth"
            :label='item.label'
            :class-name='item.label'
            :render-header='addSearch'
            show-overflow-tooltip
          >
          </el-table-column>
        </el-table-column>
        <el-table-column
          :label='item.label'
          :class-name='item.label'
          align='center'
          :width="columnwidth"
          v-else
          sortable='custom'
          show-overflow-tooltip
        >
          <el-table-column
            :prop="item.prop"
            :width="columnwidth"
            :label='item.label'
            :class-name='item.label'
            :render-header='addSearch'
            show-overflow-tooltip
          >
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
          :class-name='item.label'
          align='center'
          v-else
          sortable='custom'
          show-overflow-tooltip
        >
          <el-table-column
            :prop="item.prop"
            :label='item.label'
            :class-name='item.label'
            :render-header='addSearch'
            show-overflow-tooltip
          >
          </el-table-column>
        </el-table-column>
      </div>
      <el-table-column label='Operation' width='160px;' align='center'>
         <template slot-scope='scope'>
           <el-button size="mini" type="primary" @click="handleEdit(scope.$index, scope.row)">Edit</el-button>
           <el-button size="mini" type="danger" @click="handledelete(scope.$index, scope.row)">Delete</el-button>
          </template>
      </el-table-column>
      <!-- 选择框的显示，动态控制，根据checkbox的CheckBox的显示与否 -->
      <el-table-column label='#' align='center' v-if='showcheckbox'>
        <el-table-column label='feedback' width='110px;'>
          <template slot-scope='scope'>
            <el-button size="mini" @click="handleFeedBack(scope.$index, scope.row)">FeedBack</el-button>
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
  name: 'PoLinkTable',
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
      columnwidth: this.setWidth,
      showcheckbox: this.setCheckbox,
      colorColumns: this.setcolorColmuns,
      celllabel: this.setCellLabel,
      cellcolor: this.setCellColor,
      selectednum: [],
      ColumnsData: [],
      tableData: [],
      search: {},
      total: 0,
      selectedRowVal: []
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
    }
  },
  methods: {
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
            console.log('enter')
            this.vaildInputValue(column.label, inputValue)
          }
        }
      })
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
    handleFeedBack (index, row) {
      this.selectedRowVal = row
      this.$emit('getSelectedRowVal', this.selectedRowVal)
    },
    handleEdit (index, row) {
      this.$emit('getpoVal', row)
    },
    handledelete (index, row) {
      this.$emit('getpoValto', row)
    },
    clickPage (page) {
      // console.log(page)
      let pageNum = page
      this.$emit('getPageNum', pageNum)
    }
  }
}
</script>

<style lang='less'>
  .edittable {
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
