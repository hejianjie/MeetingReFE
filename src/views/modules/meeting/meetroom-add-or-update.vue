<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
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
      <el-form-item label="会议室" prop="roomName">
        <el-input v-model="dataForm.roomName" placeholder="会议室"></el-input>
      </el-form-item>
      <el-form-item label="所属区域" prop="roomArea">
        <el-input v-model="dataForm.roomArea" placeholder="所属区域"></el-input>
      </el-form-item>
      <el-form-item label="会议室地点" prop="location">
        <el-input
          v-model="dataForm.location"
          placeholder="会议室地点"
        ></el-input>
      </el-form-item>
      <!-- <el-form-item label="设备" prop="equipment">
        <el-input v-model="dataForm.equipment" placeholder="设备"></el-input>
      </el-form-item> -->
      <el-form-item label="拥有设备" size="mini" prop="equipment">
        <el-checkbox-group v-model="dataForm.equipment">
          <el-checkbox v-for="eq in eqList" :key="eq.id" :label="eq.name">{{
            eq.name
          }}</el-checkbox>
        </el-checkbox-group>
      </el-form-item>
      <el-form-item label="容纳人数" prop="capacity">
        <el-input v-model="dataForm.capacity" placeholder="容纳人数"></el-input>
      </el-form-item>
      <!-- <el-form-item label="会议室负责人" prop="roomLeader">
      <el-input v-model="dataForm.roomLeader" placeholder="会议室负责人"></el-input>
    </el-form-item>
    <el-form-item label="负责人联系方式" prop="roomPhone">
      <el-input v-model="dataForm.roomPhone" placeholder="负责人联系方式"></el-input>
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
  created() {
    this.geteqlist();
  },
  data() {
    return {
      visible: false,
      eqList: [],
      dataForm: {
        roomId: 0,
        roomName: "",
        roomArea: "",
        location: "",
        equipment: [],
        // eqIdList: [],
        capacity: "",
        roomLeader: "",
        roomPhone: "",
        data1: "",
        data2: "",
      },
      dataRule: {
        roomName: [
          { required: true, message: "会议室不能为空", trigger: "blur" },
        ],
        roomArea: [
          { required: true, message: "所属区域不能为空", trigger: "blur" },
        ],
        location: [
          { required: true, message: "会议室地点不能为空", trigger: "blur" },
        ],
        // equipment: [
        //   { required: true, message: "设备不能为空", trigger: "blur" },
        // ],
        capacity: [
          { required: true, message: "容纳人数不能为空", trigger: "blur" },
        ],
        // roomLeader: [
        //   { required: true, message: "会议室负责人不能为空", trigger: "blur" },
        // ],
        // roomPhone: [
        //   {
        //     required: true,
        //     message: "负责人联系方式不能为空",
        //     trigger: "blur",
        //   },
        // ],
        // data1: [
        //   { required: true, message: "预留字段1不能为空", trigger: "blur" },
        // ],
        // data2: [
        //   { required: true, message: "预留字段2不能为空", trigger: "blur" },
        // ],
      },
    };
  },
  methods: {
    geteqlist() {
      this.$http({
        url: this.$http.adornUrl("/meeting/meetroom/eqlist"),
        method: "get",
        // 设置请求头
        headers: {
          "Content-Type": "application/json",
        },
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.eqList = data.eqList;
        } else {
          this.$message.error(data.msg);
        }
      });
    },
    init(id) {
      this.geteqlist();
      this.dataForm.roomId = id || 0;
      this.visible = true;
      this.$nextTick(() => {
        this.$refs["dataForm"].resetFields();
        if (this.dataForm.roomId) {
          this.$http({
            url: this.$http.adornUrl(
              `/meeting/meetroom/info/${this.dataForm.roomId}`
            ),
            method: "get",
            params: this.$http.adornParams(),
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.roomName = data.meetRoom.roomName;
              this.dataForm.roomArea = data.meetRoom.roomArea;
              this.dataForm.location = data.meetRoom.location;
              console.log(data.meetRoom.equipment);
              if (data.meetRoom.equipment != "无") {
                this.dataForm.equipment = data.meetRoom.equipment.split(",");
              } else {
                this.dataForm.equipment = [];
              }
              //this.dataForm.equipment = data.meetRoom.equipment;
              this.dataForm.capacity = data.meetRoom.capacity;
              this.dataForm.roomLeader = data.meetRoom.roomLeader;
              this.dataForm.roomPhone = data.meetRoom.roomPhone;
              this.dataForm.data1 = data.meetRoom.data1;
              this.dataForm.data2 = data.meetRoom.data2;
            }
          });
        }
      });
    },
    // 表单提交
    dataFormSubmit() {
      this.$refs["dataForm"].validate((valid) => {
        console.log(this.dataForm.equipment.toString());
        if (valid) {
          this.$http({
            url: this.$http.adornUrl(
              `/meeting/meetroom/${!this.dataForm.roomId ? "save" : "update"}`
            ),
            method: "post",
            data: this.$http.adornData({
              roomId: this.dataForm.roomId || undefined,
              roomName: this.dataForm.roomName,
              roomArea: this.dataForm.roomArea,
              location: this.dataForm.location,
              equipment:
                this.dataForm.equipment.toString() != "无"
                  ? this.dataForm.equipment.toString()
                  : "无",
              capacity: this.dataForm.capacity,
              roomLeader: this.dataForm.roomLeader,
              roomPhone: this.dataForm.roomPhone,
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
