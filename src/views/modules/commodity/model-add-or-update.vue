<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="型号名" prop="modelName">
      <el-input v-model="dataForm.modelName" placeholder="型号名"></el-input>
    </el-form-item>
    <el-form-item label="关联品牌" prop="brandName">
      <el-select v-model="value1" filterable clearable placeholder="请选择" @change="brandChange" @clear="brandClear"> 
        <el-option
          v-for="item in brandList"
          :key="item.brandId"
          :label="item.brandName"
          :value="item.brandId"
        >
        </el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="关联系列(可为空)" prop="seriesName">
      <el-select  v-model="value2" filterable placeholder="请选择" @change="seriesChanged"> 
        <el-option
          v-for="item in seriesList"
          :key="item.seriesId"
          :label="item.seriesName"
          :value="item.seriesId"
        >
        </el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="设置图标" prop="modelIcon">
     <single-upload v-model="dataForm.modelIcon"></single-upload>
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
import SingleUpload from "@/components/upload/singleUpload";
  export default {
    components: { SingleUpload },
    data () {
      return {
        value1:'',
        value2:'',
        seriesList:[],
        brandList:[],
        visible: false,
        dataForm: {
          modelId: 0,
          modelName: '',
          brandId: '',
          seriesId: '',
          modelIcon: '',
          showStatus: ''
        },
        dataRule: {
          modelName: [
            { required: true, message: '型号名不能为空', trigger: 'blur' }
          ],
          brandId: [
            { required: true, message: '品牌id不能为空', trigger: 'blur' }
          ],

          showStatus: [
            { required: true, message: '显示状态不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.modelId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.modelId) {
            this.$http({
              url: this.$http.adornUrl(`/commodity/model/info/${this.dataForm.modelId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.modelName = data.model.modelName
                this.dataForm.brandId = data.model.brandId
                this.dataForm.seriesId = data.model.seriesId
                this.dataForm.modelIcon = data.model.modelIcon
                this.dataForm.showStatus = data.model.showStatus
                this.value1 = data.model.brandId
                this.value2 = data.model.seriesId
              }
            })
            this.getBrandData()
            this.getSeriesData()
          }
          else{
            this.getBrandData()
            this.getSeriesData()
          }
        })
      },
      brandClear(){
        this.value2 = ''
        this.getSeriesData()
      },
      brandChange(value){
        this.dataForm.brandId = value;
        this.$http({
          url: this.$http.adornUrl(`/commodity/series/brand/${value}`),
          method: 'get',
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.seriesList = data.series
          }
        })
      },
      seriesChanged(value){
        this.dataForm.seriesId = value;
        this.value1 = this.seriesList[value-1].brandId
      },
      //获取品牌数据
      getBrandData(){
        this.$http({
          url: this.$http.adornUrl(`/commodity/brand/list`),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.brandList = data.page.list 
          }
        })
      },
      //获取系列数据
      getSeriesData(){
        this.$http({
          url: this.$http.adornUrl(`/commodity/series/list`),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.seriesList = data.page.list
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/commodity/model/${!this.dataForm.modelId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'modelId': this.dataForm.modelId || undefined,
                'modelName': this.dataForm.modelName,
                'brandId': this.dataForm.brandId,
                'seriesId': this.dataForm.seriesId,
                'modelIcon': this.dataForm.modelIcon,
                'showStatus': this.dataForm.showStatus
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.value1 = ''
                    this.value2 = ''
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
