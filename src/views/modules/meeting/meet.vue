<template>
  <div class="mod-config">
    <el-form
      :inline="true"
      :model="dataForm"
      @keyup.enter.native="getDataList()"
    >
      <el-form-item>
        <span style="text-align: left; font-weight: 600; font-size: 14px"
          >选择日期:</span
        >
        <el-date-picker
          v-model="datevalue"
          type="date"
          @change="getDataList"
          placeholder="选择日期"
          value-format="yyyy-MM-dd"
        >
        </el-date-picker>
      </el-form-item>
      <el-form-item>
        <el-input
          v-model="dataForm.key"
          placeholder="请输入想要查询的信息"
          clearable
        ></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button
          v-if="isAuth('meeting:meet:save')"
          type="primary"
          @click="addHandle()"
          >新增</el-button
        >
        <el-button
          v-if="isAuth('meeting:meet:delete')"
          type="danger"
          @click="deleteHandle()"
          :disabled="dataListSelections.length <= 0"
          >批量删除</el-button
        >
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%"
    >
      <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50"
      >
      </el-table-column>
      <!-- <el-table-column
        prop="id"
        header-align="center"
        align="center"
        label="唯一标识">
      </el-table-column> -->
      <el-table-column
        prop="department"
        header-align="center"
        align="center"
        label="使用单位"
      >
      </el-table-column>
      <el-table-column
        prop="roomUser"
        header-align="center"
        align="center"
        label="预约人"
      >
      </el-table-column>
      <!-- <el-table-column
        prop="userFrom"
        header-align="center"
        align="center"
        label="预约人隶属部门">
      </el-table-column> -->
      <el-table-column
        prop="roomName"
        header-align="center"
        align="center"
        label="会议室名称"
      >
      </el-table-column>
      <el-table-column
        prop="userPhone"
        header-align="center"
        align="center"
        label="联系方式"
      >
      </el-table-column>
      <el-table-column
        prop="date"
        header-align="center"
        align="center"
        label="会议日期"
      >
      </el-table-column>
      <el-table-column
        prop="startTime"
        header-align="center"
        align="center"
        label="开始时间"
      >
      </el-table-column>
      <el-table-column
        prop="endTime"
        header-align="center"
        align="center"
        label="结束时间"
      >
      </el-table-column>
      <el-table-column
        prop="equipment"
        header-align="center"
        align="center"
        label="设备"
      >
      </el-table-column>
      <!-- <el-table-column
        prop="userNum"
        header-align="center"
        align="center"
        label="参数人数">
      </el-table-column>
      <el-table-column
        prop="users"
        header-align="center"
        align="center"
        label="参会人员">
      </el-table-column>
      <el-table-column
        prop="meetingTheme"
        header-align="center"
        align="center"
        label="会议主题">
      </el-table-column>
      <el-table-column
        prop="remark"
        header-align="center"
        align="center"
        label="备注">
      </el-table-column>
      <el-table-column
        prop="status"
        header-align="center"
        align="center"
        label="预约状态">
      </el-table-column> -->
      <!-- <el-table-column
        prop="data1"
        header-align="center"
        align="center"
        label="预留字段1">
      </el-table-column>
      <el-table-column
        prop="data2"
        header-align="center"
        align="center"
        label="预留字段2">
      </el-table-column> -->
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作"
      >
        <template slot-scope="scope">
          <el-button
            type="text"
            size="small"
            @click="addOrUpdateHandle(scope.row.id)"
            >详情</el-button
          >
          <el-button
            type="text"
            size="small"
            @click="deleteHandle(scope.row.id)"
            >删除</el-button
          >
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper"
    >
    </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update
      v-if="addOrUpdateVisible"
      ref="addOrUpdate"
      @refreshDataList="getDataList"
    ></add-or-update>
  </div>
</template>

<script>
import AddOrUpdate from "./meet-add-or-update";
export default {
  data() {
    return {
      dataForm: {
        key: "",
      },
      datevalue: "",
      dataList: [],
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      dataListLoading: false,
      dataListSelections: [],
      addOrUpdateVisible: false,
    };
  },
  components: {
    AddOrUpdate,
  },
  activated() {
    this.getDataList();
  },
  methods: {
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/meeting/meet/list"),
        method: "get",
        params: this.$http.adornParams({
          page: this.pageIndex,
          limit: this.pageSize,
          key: this.dataForm.key,
          name: "",
          date: this.datevalue,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list;
          this.totalPage = data.page.totalCount;
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    // 每页数
    sizeChangeHandle(val) {
      this.pageSize = val;
      this.pageIndex = 1;
      this.getDataList();
    },
    // 当前页
    currentChangeHandle(val) {
      this.pageIndex = val;
      this.getDataList();
    },
    // 多选
    selectionChangeHandle(val) {
      this.dataListSelections = val;
    },
    // 新增
    addHandle() {
      this.$router.replace({ path: "/home" });
    },
    // 修改
    addOrUpdateHandle(id) {
      this.addOrUpdateVisible = true;
      this.$nextTick(() => {
        this.$refs.addOrUpdate.init(id);
      });
    },
    // 删除
    deleteHandle(id) {
      var ids = id
        ? [id]
        : this.dataListSelections.map((item) => {
            return item.id;
          });
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
          url: this.$http.adornUrl("/meeting/meet/delete"),
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
};
</script>
