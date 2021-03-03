<template>
  <div class='mpsCheckTable visualList' id="scr">
    <el-table
      :data="tableData"
      border
      empty-text='No Data'
      @sort-change='sortColumns'
      @row-click='clickRow'
      ref='commontable'
      style="width: 100%">

      <!-- 为了防止循环第一项显示在最后一项 -->
      <el-table-column>
        <el-table-column
          width="1"
        >
        </el-table-column>
      </el-table-column>
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
        <!--如果没有DF列，那么PN列双倍宽度，使日期对齐-->
        <el-table-column
          :width="pnWidth == 'true' && item.label == 'PN'? 230: 115"
          :prop="item.prop"
          :label='item.label'
          v-else
          show-overflow-tooltip
        >
        </el-table-column>
      </div>
    </el-table>
  </div>
</template>

<script>
export default {
  name: 'mpsSimpleTable',
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
    setTablePnData: {
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
      cellcolor: this.setCellColor,
      selectednum: [],
      ColumnsData: [],
      tableData: [],
      search: {},
      total: 0,
      pnWidth: false,
      selectedRowVal: []
    }
  },
  watch: {
    setTablePnData: {
      handler: function (val, oldval) {
        this.ColumnsData = val.columnsData
        this.tableData = val.tableData
        this.search = val.search
        this.total = val.total
        this.pnWidth = val.pnWidth
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
    clickRow (row) {
      this.$refs.commontable.toggleRowSelection(row)
    }
  }
}
</script>

<style lang='less'>
  .mpsCheckTable {
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
  }
</style>
