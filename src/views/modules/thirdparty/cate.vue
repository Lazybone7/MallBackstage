<template>
  <div>
    <el-form
      :inline="true"
      :model="dataForm"
      @keyup.enter.native="getDataList()"
    >
      <el-form-item>
        <el-button type="primary" @click="addCateHandle()">新增</el-button>
      </el-form-item>
    </el-form>
    <add-cate
      v-if="addCateVisible"
      ref="addCate"
      @refreshDataList="getDataList"
    ></add-cate>
    <el-table
      :data="dataList"
      v-loading="dataListLoading"
      element-loading-text="数据正在路上"
      border
      style="width: 100%"
    >
      <el-table-column
        prop="iconId"
        header-align="center"
        align="center"
        label="ID"
      >
      </el-table-column>

      <el-table-column
        prop="iconName"
        header-align="center"
        align="center"
        label="名称"
      >
      </el-table-column>

      <el-table-column
        prop="iconAddress"
        header-align="center"
        align="center"
        label="图片"
      >
        <template slot-scope="scope">
          <img :src="scope.row.iconAddress" style="width: 100px; height: 80px" />
        </template>
      </el-table-column>

      <el-table-column
        prop="imgLink"
        header-align="center"
        align="center"
        label="页面链接"
      >
      </el-table-column>

      <el-table-column
        prop="iconShowStatus"
        header-align="center"
        align="center"
        label="是否显示"
      >
        <template slot-scope="scope">
          <el-switch
            v-model="scope.row.iconShowStatus"
            active-color="#13ce66"
            inactive-color="#ff4949"
            :active-value="1"
            :inactive-value="0"
            @change="updateShowStatus(scope.row)"
          ></el-switch>
        </template>
      </el-table-column>

      <el-table-column
        prop="operation"
        header-align="center"
        align="center"
        label="操作"
      >
        <template slot-scope="scope">
          <el-button
            type="text"
            size="small"
            @click="deleteCateHandle(scope.row.iconId)"
            >删除</el-button
          >
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
import AddCate from "./cate-add";
import SingleUpload from "@/components/upload/singleUpload";
export default {
  components: { SingleUpload, AddCate },
  data() {
    return {
      dataForm: {},
      addCateVisible: false,
      dataListLoading: false, //加载完成前的动画
      dataList: [], //表格数据
    };
  },

  methods: {
    getDataList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/thirdparty/cate/img"),
        method: "get",
      }).then(({ data }) => {
        this.dataListLoading = false;
        if (data && data.code === 0) {
          this.dataList = data.message;
        } else {
          this.dataList = [];
        }
      });
    },

    addCateHandle() {
      this.addCateVisible = true;
      this.$nextTick(() => {
        this.$refs.addCate.init();
      });
    },
    updateShowStatus(data) {
      console.log("data", data);
      let { iconId, iconShowStatus } = data;
      this.$http({
        url: this.$http.adornUrl("/thirdparty/cate/update/status"),
        method: "post",
        data: this.$http.adornData({ iconId, iconShowStatus }, false),
      }).then(({ data }) => {
        this.$message({
          message: `状态修改成功！`,
          type: "success",
        });
      });
    },

    deleteCateHandle(id) {
      var ids = [id];
      this.$confirm(
        `确定对[id=${ids.join(",")}]进行[${id ? "删除" : "批量删除"}]操作?`,
        "提示",
        {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning",
        }
      ).then(() => {
        this.$http({
          url: this.$http.adornUrl("/thirdparty/cate/delete"),
          method: "post",
          data: this.$http.adornData(ids, false),
        }).then(({ data }) => {
          if (data && data.code === 0) {
            this.$message({
              message: "操作成功",
              type: "success",
              duration: 1500,
              onClose: () => {
                this.getDataList();
              },
            });
          } else {
            this.$message.error(data.msg);
          }
        });
      });
    },
  },

  activated() {
    this.getDataList();
  },
};
</script>

<style>
</style>
