<template>
  <div class='filebox'>
    <span class='labeltext'>{{$t('File_Upload.File_upload')}}:</span>
    <input
     type="text"
     class='inputtext'
     v-model='filename'
    >
    <input type="button" style=" border: 1px solid rgb(78, 118, 186);background-color: rgb(78, 118, 186);" :value="$t('File_Upload.Select_file')" class='selectbutton'>
    <input
    type="file"
    multiple="multiple"
    v-if='isshowfile'
    accept=".csv, application/vnd.ms-excel, application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"
    class='selectfile'
    @change='handleFileChange'
    ref='selectfile'
    >
    <button
     class='submitbutton'
     :disabled='disabled'
     @click='handleSubmitFile'
    >{{$t('File_Upload.Submit')}}</button>
    <!--判断pcp文件是否重名-->
    <div class="deleteModel">
      <el-dialog
        title="Tips"
        :visible.sync="dialogVisible"
        width="30%"
        center
      >
        <span style="font-size: 18px;color: #C0B9A2">This file already exists. If you upload it, the data will be overwritten. Do you want to continue upload this file.</span>
        <span slot="footer" class="dialog-footer" >
          <el-button type="primary" @click="dialogVisible = false">Yes</el-button>
          <el-button @click="cancelUpload">No</el-button>
        </span>
      </el-dialog>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'File',
  props: {
    isdisabled: {
      type: Boolean,
      required: false,
      default: true
    }
  },
  watch: {
    isdisabled (val) {
      this.disabled = true
      console.log()
    }
  },
  data () {
    return {
      filename: '',
      disabled: this.isdisabled,
      isshowfile: true,
      dialogVisible: false
    }
  },
  methods: {
    handleFileChange (e) {
      // 首先获取该DOM元素
      let inputDOM = this.$refs.selectfile
      // 通过DOM元素取文件数据
      this.file = inputDOM.files[0]
      let filename = this.file.name.replace('.xlsx', '')
      if (this.$route.path === '/reportfileupload') {
        axios.get('/rest/report_digitalization/file_upload/has_imported/' + filename + '')
          .then((res) => {
            res = res.data
            // 判断report文件是否重名
            if (res.payload.hasImported === true) {
              this.dialogVisible = true
            } else {
              this.dialogVisible = false
            }
          })
      } else {
        axios.get('/rest/file_upload/has_imported/' + filename + '')
          .then((res) => {
            res = res.data
            // 判断pcp文件是否重名
            if (res.payload.hasImported === true && this.$store.state.filename === 'FTX_PCP_Q3mm_DF_yyyymmdd') {
              this.dialogVisible = true
            } else {
              this.dialogVisible = false
            }
          })
      }
      // 获取文件的名字
      this.filename = this.file.name
      if (this.filename) {
        this.disabled = false
      } else {
        this.disabled = true
      }
    },
    handleSubmitFile () {
      // 解决同名文件只上传一次的bug
      this.$emit('GetFile', this.file)
      this.isshowfile = true
    },
    // 取消上传pcp文件并刷新页面
    cancelUpload () {
      this.$store.state.language = (new Date()).getTime()
    }
  }
}
</script>
<style lang="less">
  .filebox{
    .deleteModel{
      .el-dialog--center .el-dialog__footer{
        text-align: center!important;
      }
    }
  }
</style>
<style lang='less' scoped>
  .filebox {
    /*width: 700px;*/
    position:relative;
    float: left;
    margin: 0px 10px 10px 0px;
  }
  .selectbutton{
    margin: 0px 10px;
    height: 33px;
    width: 100px;
  }
  .submitbutton{
    border: 0px!important;
    background-color: #4E76BA;
    margin: 0 5px 0 0;
    position: relative;
    z-index: 1;
    width: 72px;
  }
  .labeltext {
    margin: 0px 5px 0px 0px;
    color: #B6B6B6;
    font-size:18px;
  }
  .inputtext {
    width:150px;
    height:33px;
    padding-left: 5px;
    font-size: 14px;
    border-radius: 5px;
    background-color: #313742;
    border: 1px solid #93CEFA;
    outline:none;
    color: #D5D8DF;
    .ellipsis()
  }
  .selectfile {
    opacity: 0;
    width: 102px;
    height: 33px;
    position: absolute;
    top: 0;
    left: 268px;
  }
  // 判断pcp文件是否同名模态框
  .deleteModel .el-dialog--center{
    background-color: rgba(44, 49, 61, 1)!important;
    .el-dialog__title{
      color: red!important;
      font-size: 20px!important;
    }
    .el-button--primary{
      border: #409EFF;
      border-radius: 5px;
      background-color: #409EFF!important;
    }
    .el-button--default{
      border: #dcdfe6;
      border-radius: 5px;
      background-color: #fff!important;
      color: #606266;
    }
  }
</style>
