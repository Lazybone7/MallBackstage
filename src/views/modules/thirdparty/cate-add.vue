<template>
  <el-dialog
    title="新增图标"
    :close-on-click-modal="false"
    :visible.sync="visible"
  >
    <el-form
      :model="dataForm"
      :rules="dataRule"
      ref="dataForm"
      @keyup.enter.native="dataFormSubmit()"
      label-width="120px"
    >
      <el-form-item label="图标名" prop="iconName">
        <el-input v-model="dataForm.iconName" placeholder="图标名"></el-input>
      </el-form-item>
      <el-form-item label="上传图标" prop="iconAddress">
        <single-upload v-model="dataForm.iconAddress"></single-upload>
      </el-form-item>
      <el-form-item label="是否加入显示图标" prop="iconShowStatus">
        <el-switch
          v-model="dataForm.iconShowStatus"
          active-color="#13ce66"
          inactive-color="#ff4949"
          :active-value="1"
          :inactive-value="0"
        ></el-switch>
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
  data() {
    return {
      visible: false,
      dataForm: {
        iconName:"",
        iconAddress: "",
        iconShowStatus: 1,
      },
      dataRule: {
        logo: [
          { required: true, message: "品牌logo地址不能为空", trigger: "blur" },
        ],
        showStatus: [
          {
            required: true,
            message: "显示状态[0-不显示；1-显示]不能为空",
            trigger: "blur",
          },
        ],
      },
    };
  },
  methods: {
    init() {
      this.visible = true;
    },
    // 表单提交
    dataFormSubmit() {
      this.$refs["dataForm"].validate((valid) => {
        this.$http({
          url: this.$http.adornUrl("/thirdparty/cate/save"),
          method: "post",
          data: this.$http.adornData({
            iconId: 0,
            iconName:this.dataForm.iconName, 
            iconAddress: this.dataForm.iconAddress,
            iconShowStatus: this.dataForm.iconShowStatus,
          }),
        }).then(({ data }) => {
          if (data && data.code === 0) {
            this.$message({
              message: "操作成功",
              type: "success",
              duration: 1500,
              onClose: () => {
                this.visible = false;
                this.$emit("refreshDataList");
              },
            });
          } else {
            this.$message.error(data.msg);
          }
        });
      });
    },
  },
};
</script>
