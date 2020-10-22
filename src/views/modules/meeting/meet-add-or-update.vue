<template>
  <el-dialog
    :title="'详情'"
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
      <el-form-item label="使用单位" prop="department">
        <el-input v-model="dataForm.department" readonly></el-input>
      </el-form-item>
      <el-form-item label="预约人" prop="roomUser">
        <el-input v-model="dataForm.roomUser" readonly></el-input>
      </el-form-item>
      <el-form-item label="隶属部门" prop="userFrom">
        <el-input v-model="dataForm.userFrom" readonly></el-input>
      </el-form-item>
      <el-form-item label="会议室名称" prop="roomName">
        <el-input v-model="dataForm.roomName" readonly></el-input>
      </el-form-item>
      <el-form-item label="设备" prop="equipment">
        <el-input v-model="dataForm.equipment" readonly></el-input>
      </el-form-item>
      <el-form-item label="联系方式" prop="userPhone">
        <el-input v-model="dataForm.userPhone" readonly></el-input>
      </el-form-item>
      <el-form-item label="会议日期" prop="date">
        <el-input v-model="dataForm.date" readonly></el-input>
      </el-form-item>
      <!-- <el-form-item label="开始时间" prop="startTime">
        <el-input
          v-model="dataForm.startTime"
          placeholder="开始时间"
        ></el-input>
      </el-form-item>
      <el-form-item label="结束时间" prop="endTime">
        <el-input
          v-model="dataForm.endTime"
          placeholder="结束时间"
        ></el-input>
      </el-form-item> -->
      <el-form-item label="参会人数" prop="userNum">
        <el-input v-model="dataForm.userNum" readonly></el-input>
      </el-form-item>
      <el-form-item label="参会人员" prop="users">
        <el-input v-model="dataForm.users" readonly></el-input>
      </el-form-item>
      <el-form-item label="会议主题" prop="meetingTheme">
        <el-input v-model="dataForm.meetingTheme" readonly></el-input>
      </el-form-item>
      <el-form-item label="备注" prop="remark">
        <el-input v-model="dataForm.remark" readonly></el-input>
      </el-form-item>
      <!-- <el-form-item label="预约状态" prop="status">
        <el-input v-model="dataForm.status" placeholder="预约状态"></el-input>
      </el-form-item>
      <el-form-item label="预留字段1" prop="data1">
        <el-input v-model="dataForm.data1" placeholder="预留字段1"></el-input>
      </el-form-item>
      <el-form-item label="预留字段2" prop="data2">
        <el-input v-model="dataForm.data2" placeholder="预留字段2"></el-input>
      </el-form-item> -->
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
export default {
  data() {
    return {
      visible: false,
      dataForm: {
        id: 0,
        department: "",
        roomUser: "",
        userFrom: "",
        roomName: "",
        equipment: "",
        userPhone: "",
        date: "",
        startTime: "",
        endTime: "",
        userNum: "",
        users: "",
        meetingTheme: "",
        remark: "",
        status: "",
        data1: "",
        data2: "",
      },
      dataRule: {
        department: [
          {
            required: true,
            message: "使用单位（例:软件学院）不能为空",
            trigger: "blur",
          },
        ],
        roomUser: [
          { required: true, message: "会议室预约人不能为空", trigger: "blur" },
        ],
        userFrom: [
          {
            required: true,
            message: "预约人隶属部门不能为空",
            trigger: "blur",
          },
        ],
        roomName: [
          { required: true, message: "会议室名称不能为空", trigger: "blur" },
        ],
        equipment: [
          { required: true, message: "设备不能为空", trigger: "blur" },
        ],
        userPhone: [
          {
            required: true,
            message: "预约人联系方式不能为空",
            trigger: "blur",
          },
        ],
        date: [
          { required: true, message: "会议日期不能为空", trigger: "blur" },
        ],
        startTime: [
          { required: true, message: "会议开始时间不能为空", trigger: "blur" },
        ],
        endTime: [
          { required: true, message: "会议结束时间不能为空", trigger: "blur" },
        ],
        userNum: [
          { required: true, message: "参数人数不能为空", trigger: "blur" },
        ],
        users: [
          { required: true, message: "参会人员不能为空", trigger: "blur" },
        ],
        meetingTheme: [
          { required: true, message: "会议主题不能为空", trigger: "blur" },
        ],
        remark: [{ required: true, message: "备注不能为空", trigger: "blur" }],
        status: [
          { required: true, message: "预约状态不能为空", trigger: "blur" },
        ],
        data1: [
          { required: true, message: "预留字段1不能为空", trigger: "blur" },
        ],
        data2: [
          { required: true, message: "预留字段2不能为空", trigger: "blur" },
        ],
      },
    };
  },
  methods: {
    init(id) {
      this.dataForm.id = id || 0;
      this.visible = true;
      this.$nextTick(() => {
        this.$refs["dataForm"].resetFields();
        if (this.dataForm.id) {
          this.$http({
            url: this.$http.adornUrl(`/meeting/meet/info/${this.dataForm.id}`),
            method: "get",
            params: this.$http.adornParams(),
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.department = data.meet.department;
              this.dataForm.roomUser = data.meet.roomUser;
              this.dataForm.userFrom = data.meet.userFrom;
              this.dataForm.roomName = data.meet.roomName;
              this.dataForm.equipment = data.meet.equipment;
              this.dataForm.userPhone = data.meet.userPhone;
              this.dataForm.date = data.meet.date;
              this.dataForm.startTime = data.meet.startTime;
              this.dataForm.endTime = data.meet.endTime;
              this.dataForm.userNum = data.meet.userNum;
              this.dataForm.users = data.meet.users;
              this.dataForm.meetingTheme = data.meet.meetingTheme;
              this.dataForm.remark = data.meet.remark;
              this.dataForm.status = data.meet.status;
              this.dataForm.data1 = data.meet.data1;
              this.dataForm.data2 = data.meet.data2;
            }
          });
        }
      });
    },
    // 表单提交
    dataFormSubmit() {
      this.$refs["dataForm"].validate((valid) => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl(
              `/meeting/meet/${!this.dataForm.id ? "save" : "update"}`
            ),
            method: "post",
            data: this.$http.adornData({
              id: this.dataForm.id || undefined,
              department: this.dataForm.department,
              roomUser: this.dataForm.roomUser,
              userFrom: this.dataForm.userFrom,
              roomName: this.dataForm.roomName,
              equipment: this.dataForm.equipment,
              userPhone: this.dataForm.userPhone,
              date: this.dataForm.date,
              startTime: this.dataForm.startTime,
              endTime: this.dataForm.endTime,
              userNum: this.dataForm.userNum,
              users: this.dataForm.users,
              meetingTheme: this.dataForm.meetingTheme,
              remark: this.dataForm.remark,
              status: this.dataForm.status,
              data1: this.dataForm.data1,
              data2: this.dataForm.data2,
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
        }
      });
    },
  },
};
</script>
