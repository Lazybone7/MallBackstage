<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="" prop="accountName">
      <el-input v-model="dataForm.accountName" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="password">
      <el-input v-model="dataForm.password" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="nickName">
      <el-input v-model="dataForm.nickName" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="phoneNum">
      <el-input v-model="dataForm.phoneNum" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="img">
      <el-input v-model="dataForm.img" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="gender">
      <el-input v-model="dataForm.gender" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="birth">
      <el-input v-model="dataForm.birth" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="province">
      <el-input v-model="dataForm.province" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="city">
      <el-input v-model="dataForm.city" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="region">
      <el-input v-model="dataForm.region" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="job">
      <el-input v-model="dataForm.job" placeholder=""></el-input>
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
          id: 0,
          accountName: '',
          password: '',
          nickName: '',
          phoneNum: '',
          img: '',
          gender: '',
          birth: '',
          province: '',
          city: '',
          region: '',
          job: ''
        },
        dataRule: {
          accountName: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          password: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          nickName: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          phoneNum: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          img: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          gender: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          birth: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          province: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          city: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          region: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          job: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/springboot/member/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.accountName = data.member.accountName
                this.dataForm.password = data.member.password
                this.dataForm.nickName = data.member.nickName
                this.dataForm.phoneNum = data.member.phoneNum
                this.dataForm.img = data.member.img
                this.dataForm.gender = data.member.gender
                this.dataForm.birth = data.member.birth
                this.dataForm.province = data.member.province
                this.dataForm.city = data.member.city
                this.dataForm.region = data.member.region
                this.dataForm.job = data.member.job
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
              url: this.$http.adornUrl(`/springboot/member/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'accountName': this.dataForm.accountName,
                'password': this.dataForm.password,
                'nickName': this.dataForm.nickName,
                'phoneNum': this.dataForm.phoneNum,
                'img': this.dataForm.img,
                'gender': this.dataForm.gender,
                'birth': this.dataForm.birth,
                'province': this.dataForm.province,
                'city': this.dataForm.city,
                'region': this.dataForm.region,
                'job': this.dataForm.job
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
