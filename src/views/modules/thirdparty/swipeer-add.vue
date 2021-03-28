<template>
  <el-dialog
    title="新增图片"
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
      <el-form-item label="上传图片" prop="img">
        <single-upload v-model="dataForm.img"></single-upload>
      </el-form-item>
      <el-form-item label="是否加入轮播图" prop="showStatus">
        <el-switch
          v-model="dataForm.showStatus"
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
        img: "",
        showStatus: 1,
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
          url: this.$http.adornUrl("/thirdparty/swiper/save"),
          method: "post",
          data: this.$http.adornData({
            id: 0,
            imgSrc: this.dataForm.img,
            showStatus: this.dataForm.showStatus,
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
      //     if (valid) {
      //       this.$http({
      //         url: this.$http.adornUrl(
      //           `/product/brand/${!this.dataForm.brandId ? "save" : "update"}`
      //         ),
      //         method: "post",
      //         data: this.$http.adornData({
      //           brandId: this.dataForm.brandId || undefined,
      //           name: this.dataForm.name,
      //           logo: this.dataForm.logo,
      //           descript: this.dataForm.descript,
      //           showStatus: this.dataForm.showStatus,
      //           firstLetter: this.dataForm.firstLetter,
      //           sort: this.dataForm.sort,
      //         }),
      //       }).then(({ data }) => {
      //         if (data && data.code === 0) {
      //           this.$message({
      //             message: "操作成功",
      //             type: "success",
      //             duration: 1500,
      //             onClose: () => {
      //               this.visible = false;
      //               this.$emit("refreshDataList");
      //             },
      //           });
      //         } else {
      //           this.$message.error(data.msg);
      //         }
      //       });
      //     }
    },
  },
};
</script>
