<template>
  <div class="mod-user">
    <el-form
      :inline="true"
      :model="dataForm"
      @keyup.enter.native="getDataList()"
    >
      <el-form-item>
        <el-input
          v-model="dataForm.userName"
          placeholder="请输入用户名"
          clearable
        ></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button
          v-if="isAuth('sys:user:save')"
          type="primary"
          @click="addOrUpdateHandle()"
          >新增用户</el-button
        >
        <el-button
          v-if="isAuth('sys:user:delete')"
          type="danger"
          @click="deleteHandle()"
          :disabled="dataListSelections.length <= 0"
          >批量删除</el-button
        >
      </el-form-item>
      <el-form-item class="temp">
        <a href="../../../../static/file/template.xlsx" download="template.xlsx"
          >下载模板</a
        >
      </el-form-item>
      <el-form-item>
        <el-upload
          class="upload-demo"
          ref="upload"
          :action="uploadUrl()"
          accept=".xls, .xlsx"
          :on-preview="handlePreview"
          :on-remove="handleRemove"
          :file-list="fileList"
          :before-upload="beforeAvatarUpload"
          :on-success="handleAvatarSuccess"
        >
          <el-button slot="trigger" type="primary" title="只能上传excel文件"
            >导入文件</el-button
          >
        </el-upload>
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
      <el-table-column
        prop="userId"
        header-align="center"
        align="center"
        width="80"
        label="ID"
      >
      </el-table-column>
      <el-table-column
        prop="username"
        header-align="center"
        align="center"
        label="账户"
      >
      </el-table-column>
      <el-table-column
        prop="thename"
        header-align="center"
        align="center"
        label="用户名"
      >
      </el-table-column>
      <el-table-column
        prop="department"
        header-align="center"
        align="center"
        label="所属部门"
      >
      </el-table-column>
      <el-table-column
        prop="mobile"
        header-align="center"
        align="center"
        label="手机号"
      >
      </el-table-column>
      <el-table-column
        prop="status"
        header-align="center"
        align="center"
        label="状态"
      >
        <template slot-scope="scope">
          <el-tag v-if="scope.row.status === 0" size="small" type="danger"
            >禁用</el-tag
          >
          <el-tag v-else size="small">正常</el-tag>
        </template>
      </el-table-column>
      <el-table-column
        prop="createTime"
        header-align="center"
        align="center"
        width="180"
        label="创建时间"
      >
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作"
      >
        <template slot-scope="scope">
          <el-button
            v-if="isAuth('sys:user:update')"
            type="text"
            size="small"
            @click="addOrUpdateHandle(scope.row.userId)"
            >修改</el-button
          >
          <el-button
            v-if="isAuth('sys:user:delete')"
            type="text"
            size="small"
            @click="deleteHandle(scope.row.userId)"
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
import AddOrUpdate from "./user-add-or-update";
export default {
  data() {
    return {
      dataForm: {
        userName: "",
      },
      fileName: "",
      fileList: [],
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
    handleAvatarSuccess() {
      console.log(555555);
      this.getDataList();
      this.$refs["upload"].clearFiles();
    },
    // Excel批量增加用户
    uploadUrl: function () {
      return this.$http.adornUrl(
        `/sys/user/excelupdate?token=${this.$cookie.get("token")}`
      );
    },
    uploadSuccess(response, file, fileList) {
      if (response.status) {
        alert("文件导入成功");
      } else {
        alert("文件导入失败");
      }
    },
    uploadFalse(response, file, fileList) {
      alert("文件上传失败！");
    },
    // 上传前对文件的大小的判断
    beforeAvatarUpload(file) {
      this.fileName = file.name;
      console.log("文件名" + this.fileName);
      const extension = file.name.split(".")[1] === "xls";
      const extension2 = file.name.split(".")[1] === "xlsx";
      const extension3 = file.name.split(".")[1] === "doc";
      const extension4 = file.name.split(".")[1] === "docx";
      const isLt2M = file.size / 1024 / 1024 < 10;
      if (!extension && !extension2 && !extension3 && !extension4) {
        alert("上传模板只能是 xls、xlsx、doc、docx 格式!");
      }
      if (!isLt2M) {
        console.log("上传模板大小不能超过 10MB!");
      }
      return extension || extension2 || extension3 || (extension4 && isLt2M);
    },
    submitUpload() {
      if (this.businessType != null) {
        //触发组件的action
        this.$refs.upload.submit();
      }
      if (this.businessType == null) {
        this.businessType = "businessType不能为空";
      }
    },
    handleRemove(file, fileList) {
      console.log(file, fileList);
    },
    handlePreview(file) {
      if (file.response.status) {
        alert("此文件导入成功");
      } else {
        alert("此文件导入失败");
      }
    },

    // 获取数据列表
    getDataList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/sys/user/list"),
        method: "get",
        params: this.$http.adornParams({
          page: this.pageIndex,
          limit: this.pageSize,
          username: this.dataForm.userName,
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
    // 新增 / 修改
    addOrUpdateHandle(id) {
      this.addOrUpdateVisible = true;
      this.$nextTick(() => {
        this.$refs.addOrUpdate.init(id);
      });
    },
    // 删除
    deleteHandle(id) {
      var userIds = id
        ? [id]
        : this.dataListSelections.map((item) => {
            return item.userId;
          });
      this.$confirm(`确定进行[${id ? "删除" : "批量删除"}]操作?`, "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          this.$http({
            url: this.$http.adornUrl("/sys/user/delete"),
            method: "post",
            data: this.$http.adornData(userIds, false),
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
        })
        .catch(() => {});
    },
  },
};
</script>
<style >
.temp {
  margin: 0;
  padding: 0;
  width: 100px;
  height: 36px;
  background: #318df7;
  border-radius: 5px;
  text-align: center;
  transition: color 0.5s;
}
a,
a:hover,
a:link {
  text-decoration: none;
  color: white;
  font-size: 14px;
}
.temp:hover {
  background-color: #5ea5f9;
}
</style>