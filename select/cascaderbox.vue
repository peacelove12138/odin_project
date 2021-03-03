<template>
  <div class='icascader'>
    <Cascader
     :data='list'
     :placeholder='placeholdertext'
     v-model='value'
     trigger="hover"
     filterable
     @on-change='getSelectedData'
    ></Cascader>
  </div>
</template>

<script>
export default {
  name: 'CascaderBox',
  props: {
    placeholder: {
      type: String,
      required: false,
      default: 'Select'
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
      value: [],
      placeholdertext: this.placeholder,
      list: []
    }
  },
  methods: {
    getSelectedData (value) {
      // console.log(value)
      this.value = value
      this.$emit('getSelectedData', this.value)
    }
  }
}
</script>

<style lang='less'>
.icascader {
  width:200px;
  float: left;
  .ivu-input-wrapper {
    height:40px;
  }
  .ivu-input {
    height:40px;
    line-height:40px;
    padding-left:10px;
    border: 1px solid #93CEFA;
    border-radius: 20px;
    color: @basefontcolor;
    background-color: @background;
  }
  .ivu-cascader-label {
    line-height:40px;
    color: @basefontcolor;
  }
  .ivu-cascader-menu {
    background-color: @background;
  }
  .ivu-cascader .ivu-cascader-menu-item {
    color: @basefontcolor;
  }
  .ivu-cascader .ivu-cascader-menu-item:hover {
    color: @basefontcolor;
    background-color: @domhover;
  }
  .ivu-cascader-menu .ivu-cascader-menu-item-active {
    color:@basefontcolor;
    background-color: @domhover;
  }
}
</style>
