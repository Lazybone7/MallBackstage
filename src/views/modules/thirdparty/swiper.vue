<template>
  <div>
    <el-form
      :inline="true"
      :model="dataForm"
      @keyup.enter.native="getDataList()"
    >
      <el-form-item>
        <el-button type="primary" @click="addSwiperHandle()">新增</el-button>
      </el-form-item>
    </el-form>
    <add-swiper
      v-if="addSwiperVisible"
      ref="addSwiper"
      @refreshDataList="getDataList"
    ></add-swiper>
    <el-table
      :data="dataList"
      v-loading="dataListLoading"
      element-loading-text="数据正在路上"
      border
      style="width: 100%"
    >
      <el-table-column
        prop="id"
        header-align="center"
        align="center"
        label="ID"
      >
      </el-table-column>
      <el-table-column
        prop="imgSrc"
        header-align="center"
        align="center"
        label="图片"
      >
        <template slot-scope="scope">
          <img :src="scope.row.imgSrc" style="width: 100px; height: 80px" />
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
        prop="showStatus"
        header-align="center"
        align="center"
        label="是否显示"
      >
        <template slot-scope="scope">
          <el-switch
            v-model="scope.row.showStatus"
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
            @click="deleteSwiperHandle(scope.row.id)"
            >删除</el-button
          >
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
import AddSwiper from "./swipeer-add";
import SingleUpload from "@/components/upload/singleUpload";
export default {
  components: { SingleUpload, AddSwiper },
  data() {
    return {
      dataForm: {},
      addSwiperVisible: false,
      dataListLoading: false, //加载完成前的动画
      dataList: [], //表格数据
    };
  },
  methods: {
    getDataList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/thirdparty/swiper/img"),
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
    addSwiperHandle() {
      this.addSwiperVisible = true;
      this.$nextTick(() => {
        this.$refs.addSwiper.init();
      });
    },
    updateShowStatus(data) {
      console.log("data", data);
      let { id, showStatus } = data;
      this.$http({
        url: this.$http.adornUrl("/thirdparty/swiper/update/status"),
        method: "post",
        data: this.$http.adornData({ id, showStatus }, false),
      }).then(({ data }) => {
        this.$message({
          message: `状态修改成功！`,
          type: "success",
        });
      });
    },

    deleteSwiperHandle(id) {
      var ids = [id];
      this.$confirm(
        `确定对[id=${ids.join(",")}]进行[${id ? "删除" : "批量删除"}]操作?`,
        "提示",
        {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning"
        }
      ).then(() => {
        this.$http({
          url: this.$http.adornUrl("/thirdparty/swiper/delete"),
          method: "post",
          data: this.$http.adornData(ids, false)
        }).then(({ data }) => {
          if (data && data.code === 0) {
            this.$message({
              message: "操作成功",
              type: "success",
              duration: 1500,
              onClose: () => {
                this.getDataList();
              }
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
