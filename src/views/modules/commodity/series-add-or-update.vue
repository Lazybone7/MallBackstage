<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="系列名" prop="seriesName">
      <el-input v-model="dataForm.seriesName" placeholder="系列名"></el-input>
    </el-form-item>
    <el-form-item label="关联品牌" prop="brandId">
      <el-select v-model="value" filterable placeholder="请选择"> 
        <el-option
          v-for="item in seriesList"
          :key="item.seriesId"
          :label="item.brandName"
          :value="item.brandId"
        >
        </el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="显示状态" prop="showStatus">
      <el-input v-model="dataForm.showStatus" placeholder="显示状态"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        value:'',
        seriesList:[],
        visible: false,
        dataForm: {
          seriesId: 0,
          seriesName: '',
          brandId: '',
          showStatus: ''
        },
        dataRule: {
          seriesName: [
            { required: true, message: '系列名不能为空', trigger: 'blur' }
          ],
          brandId: [
            { required: true, message: '品牌名不能为空', trigger: 'blur' }
          ],
          showStatus: [
            { required: true, message: '显示状态不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.seriesId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.seriesId) {
            this.$http({
              url: this.$http.adornUrl(`/commodity/series/info/${this.dataForm.seriesId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.seriesName = data.series.seriesName
                this.dataForm.brandId = data.series.brandId
                this.dataForm.showStatus = data.series.showStatus
                this.value = data.series.brandName
              }
            })
            this.$http({
              url: this.$http.adornUrl(`/commodity/brand/list`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.seriesList = data.page.list
              }
            })
          }
          else{
            this.$http({
              url: this.$http.adornUrl(`/commodity/brand/list`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.seriesList = data.page.list
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.dataForm.brandId = this.value;
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/commodity/series/${!this.dataForm.seriesId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'seriesId': this.dataForm.seriesId || undefined,
                'seriesName': this.dataForm.seriesName,
                'brandId': this.dataForm.brandId,
                'showStatus': this.dataForm.showStatus
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.value=''
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
