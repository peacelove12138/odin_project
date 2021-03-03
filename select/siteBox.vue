<template>
  <div
    class='sitebox'
    :style='widthchange'
  >
    <!-- 多选的情况 -->
    <i-select
      v-if='ismultiple'
      v-model='selecteddata'
      :placeholder='placeholdertext'
      filterable
      multiple
      @on-change='getSelectedData'
    >
      <i-option
        v-for ='item in list'
        :value='item.value'
        :key='item.id'
      >{{item.label}}
      </i-option>
    </i-select>
    <!-- 非多选的情况 -->
    <i-select
      v-if='!ismultiple'
      v-model='selecteddata'
      :placeholder='placeholdertext'
      filterable
      clearable
      @on-change='getSelectedData'
    >
      <i-option
        v-for ='item in list'
        :value='item.value'
        :key='item.id'
      >{{item.label}}
      </i-option>
    </i-select>
  </div>
</template>

<script>
export default {
  name: 'siteBox',
  props: {
    intersectionSite: {
      type: Array,
      required: true
    },
    placeholder: {
      type: String,
      required: false,
      default: 'Select'
    },
    multiple: {
      type: Boolean,
      required: false,
      default: true
    },
    width: {
      type: String,
      required: false,
      default: '200px'
    },
    optionlist: Array
  },
  watch: {
    optionlist (val) {
      this.list = val
      this.selecteddata = this.selectedData
    }
  },
  data () {
    return {
      placeholdertext: this.placeholder,
      list: this.optionlist,
      ismultiple: this.multiple,
      widthchange: {
        width: this.width
      },
      selecteddata: ''
    }
  },
  computed: {
    selectedData () {
      return this.intersectionSite
    }
  },
  methods: {
    getSelectedData (value) {
      if (value) {
        this.selecteddata = value
      } else {
        this.selecteddata = ''
      }
      this.$emit('getSelectedData', this.selecteddata)
    }
  }
}
</script>

<style lang='less'>
  .sitebox {
    width: 130px !important;
    margin-right: 5px;
    box-sizing: border-box;
    float: left;
    /*padding: 0 10px;*/
    margin-bottom: 10px;
    .ivu-select-single .ivu-select-selection  {
       height: 40px
    }
    .ivu-select-input {
      height:40px;
      line-height: 40px;
      color: @basefontcolor;
    }
    .ivu-select-selection {
      border: 1px solid #93CEFA;
      border-radius: 20px;
      background-color: @background;
    }
    .ivu-select-multiple .ivu-select-input {
      height:35px;
    }
    .ivu-select-dropdown {
      background-color: @backgroundColor;
    }
    .ivu-select-item {
      color: @basefontcolor;
    }
    .ivu-select-item:hover {
      background-color: @background;
      color: @basefontcolor;
    }
    .ivu-select-multiple .ivu-select-item-selected {
      background-color: @backgroundColor;
      color: @basefontcolor;
    }
    .ivu-select-multiple .ivu-select-item-focus, .ivu-select-multiple .ivu-select-item-selected:hover {
      background-color: @domhover;
    }
    .ivu-tag {
      background-color: @domhover;
      border:1px solid @bordercolor;
    }
    .ivu-tag .ivu-icon-ios-close-empty {
      color: #CCC;
    }
    .ivu-tag-text {
      color: #CCC;
    }
  }
</style>
