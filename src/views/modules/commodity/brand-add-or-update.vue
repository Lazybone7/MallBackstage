<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="品牌名" prop="brandName">
      <el-input v-model="dataForm.brandName" placeholder="品牌名"></el-input>
    </el-form-item>
    <el-form-item label="关联分类" prop="catId">
        <el-select v-model="value" filterable placeholder="请选择"> 
          <el-option
            v-for="item in dataForm.catNameList"
            :key="item.catId"
            :label="item.catName"
            :value="item.catId"
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
        visible: false,
        dataForm: {
          brandId: 0,
          brandName: '',
          catId: '',
          showStatus: '',
          catName:'',
          catNameList:[]
        },
        dataRule: {
          brandName: [
            { required: true, message: '品牌名不能为空', trigger: 'blur' }
          ],
          catId: [
            { required: true, message: '所属分类不能为空', trigger: 'blur' }
          ],
          showStatus: [
            { required: true, message: '显示状态不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.brandId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.brandId) {
            this.$http({
              url: this.$http.adornUrl(`/commodity/brand/info/${this.dataForm.brandId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.brandName = data.brand.brandName
                this.dataForm.catId = data.brand.catId
                this.dataForm.showStatus = data.brand.showStatus
              }
            })
          }
          else{
            this.$http({
              url: this.$http.adornUrl(`/commodity/category/list`),
              method: 'get',
            }).then(({data})=>{
                this.dataForm.catNameList = data.page.list
            })  
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.dataForm.catId = this.value;
        this.dataForm.catName = this.value;
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/commodity/brand/${!this.dataForm.brandId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'brandId': this.dataForm.brandId || undefined,
                'brandName': this.dataForm.brandName,
                'catId': this.dataForm.catId,
                'showStatus': this.dataForm.showStatus
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
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
