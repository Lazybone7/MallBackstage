<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="商品标题" prop="commodityTitle">
      <el-input v-model="dataForm.commodityTitle" placeholder="商品标题"></el-input>
    </el-form-item>
    <el-form-item label="商品描述" prop="commodityDescription">
      <el-input v-model="dataForm.commodityDescription" placeholder="商品描述"></el-input>
    </el-form-item>
    <el-form-item label="型号id" prop="modelId">
      <el-input v-model="dataForm.modelId" placeholder="型号id"></el-input>
    </el-form-item>
    <el-form-item label="发布者id" prop="memberId">
      <el-input v-model="dataForm.memberId" placeholder="发布者id"></el-input>
    </el-form-item>
    <el-form-item label="商品价格" prop="price">
      <el-input v-model="dataForm.price" placeholder="商品价格"></el-input>
    </el-form-item>
    <el-form-item label="入手价" prop="startingPrice">
      <el-input v-model="dataForm.startingPrice" placeholder="入手价"></el-input>
    </el-form-item>
    <el-form-item label="运费" prop="shipping">
      <el-input v-model="dataForm.shipping" placeholder="运费"></el-input>
    </el-form-item>
    <el-form-item label="点赞数" prop="countLike">
      <el-input v-model="dataForm.countLike" placeholder="点赞数"></el-input>
    </el-form-item>
    <el-form-item label="收藏数" prop="countFavorites">
      <el-input v-model="dataForm.countFavorites" placeholder="收藏数"></el-input>
    </el-form-item>
    <el-form-item label="上架时间" prop="addTime">
      <el-input v-model="dataForm.addTime" placeholder="上架时间"></el-input>
    </el-form-item>
    <el-form-item label="下架时间" prop="moveTime">
      <el-input v-model="dataForm.moveTime" placeholder="下架时间"></el-input>
    </el-form-item>
    <el-form-item label="审核状态" prop="checkStatus">
      <el-input v-model="dataForm.checkStatus" placeholder="审核状态"></el-input>
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
        visible: false,
        dataForm: {
          commodityId: 0,
          commodityTitle: '',
          commodityDescription: '',
          modelId: '',
          memberId: '',
          price: '',
          startingPrice: '',
          shipping: '',
          countLike: '',
          countFavorites: '',
          addTime: '',
          moveTime: '',
          checkStatus: '',
          showStatus: ''
        },
        dataRule: {
          commodityTitle: [
            { required: true, message: '商品标题不能为空', trigger: 'blur' }
          ],
          commodityDescription: [
            { required: true, message: '商品描述不能为空', trigger: 'blur' }
          ],
          modelId: [
            { required: true, message: '型号id不能为空', trigger: 'blur' }
          ],
          memberId: [
            { required: true, message: '发布者id不能为空', trigger: 'blur' }
          ],
          price: [
            { required: true, message: '商品价格不能为空', trigger: 'blur' }
          ],
          startingPrice: [
            { required: true, message: '入手价不能为空', trigger: 'blur' }
          ],
          shipping: [
            { required: true, message: '运费不能为空', trigger: 'blur' }
          ],
          countLike: [
            { required: true, message: '点赞数不能为空', trigger: 'blur' }
          ],
          countFavorites: [
            { required: true, message: '收藏数不能为空', trigger: 'blur' }
          ],
          addTime: [
            { required: true, message: '上架时间不能为空', trigger: 'blur' }
          ],
          moveTime: [
            { required: true, message: '下架时间不能为空', trigger: 'blur' }
          ],
          checkStatus: [
            { required: true, message: '审核状态不能为空', trigger: 'blur' }
          ],
          showStatus: [
            { required: true, message: '显示状态不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.commodityId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.commodityId) {
            this.$http({
              url: this.$http.adornUrl(`/commodity/commodity/info/${this.dataForm.commodityId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.commodityTitle = data.commodity.commodityTitle
                this.dataForm.commodityDescription = data.commodity.commodityDescription
                this.dataForm.modelId = data.commodity.modelId
                this.dataForm.memberId = data.commodity.memberId
                this.dataForm.price = data.commodity.price
                this.dataForm.startingPrice = data.commodity.startingPrice
                this.dataForm.shipping = data.commodity.shipping
                this.dataForm.countLike = data.commodity.countLike
                this.dataForm.countFavorites = data.commodity.countFavorites
                this.dataForm.addTime = data.commodity.addTime
                this.dataForm.moveTime = data.commodity.moveTime
                this.dataForm.checkStatus = data.commodity.checkStatus
                this.dataForm.showStatus = data.commodity.showStatus
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/commodity/commodity/${!this.dataForm.commodityId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'commodityId': this.dataForm.commodityId || undefined,
                'commodityTitle': this.dataForm.commodityTitle,
                'commodityDescription': this.dataForm.commodityDescription,
                'modelId': this.dataForm.modelId,
                'memberId': this.dataForm.memberId,
                'price': this.dataForm.price,
                'startingPrice': this.dataForm.startingPrice,
                'shipping': this.dataForm.shipping,
                'countLike': this.dataForm.countLike,
                'countFavorites': this.dataForm.countFavorites,
                'addTime': this.dataForm.addTime,
                'moveTime': this.dataForm.moveTime,
                'checkStatus': this.dataForm.checkStatus,
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
