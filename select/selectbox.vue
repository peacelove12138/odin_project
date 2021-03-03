<template>
  <div
   :class='selectionType'
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
  name: 'SelectBox',
  props: {
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
    }
  },
  data () {
    return {
      selectionType: 'iselect',
      disabled: this.$store.state.disable,
      placeholdertext: this.placeholder,
      list: this.optionlist,
      ismultiple: this.multiple,
      widthchange: {
        width: this.width
      },
      selecteddata: []
    }
  },
  mounted () {
    this.getSelectWidth()
  },
  methods: {
    getSelectedData (value) {
      if (value) {
        this.selecteddata = value
        this.$store.commit('filename', this.selecteddata)
      } else {
        this.selecteddata = ''
      }
      this.$emit('getSelectedData', this.selecteddata)
    },
    getSelectWidth () {
      if (!this.multiple) {
        this.selectionType = 'iselects'
      } else {
        this.selectionType = 'iselect'
      }
    }
  }
}
</script>

<style lang='less'>
.iselect {
  width: 145px!important;
  box-sizing: border-box;
  float: left;
  padding: 0 10px;
  .ivu-select-single .ivu-select-selection  {
      height: 33px
  }
  .ivu-select-input {
    width: 135px;
    height:33px;
    line-height: 40px;
    color: @basefontcolor;
  }
  .ivu-select-selection {
    width: 135px!important;
    border: 1px solid #93CEFA;
    border-radius: 20px;
    background-color: @background;
  }
  .ivu-select-multiple .ivu-select-input {
    height:33px;
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
.iselects {
  box-sizing: border-box;
  float: left;
  padding: 0 10px;
  margin: 0 0 10px 10px;
  .ivu-select-single .ivu-select-selection  {
    height: 33px
  }
  .ivu-select-input {
    height:33px;
    line-height: 40px;
    color: @basefontcolor;
  }
  .ivu-select-selection {
    height: 33px!important;
    border: 1px solid #93CEFA!important;
    border-radius: 20px;
    background-color: @background;
  }
  .ivu-select-multiple .ivu-select-input {
    height:33px;
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
