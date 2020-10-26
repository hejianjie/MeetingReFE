<template>
  <div>
    <el-row>
      <el-col :span="16">
        <el-date-picker
          v-model="datevalue"
          type="date"
          @change="getTanleMsg"
          placeholder="选择日期"
          value-format="yyyy-MM-dd">
          <!-- :picker-options="expireTimeOption" -->
        </el-date-picker>
        <el-table
          class="customer-table"
          :data="tableData"
          :cell-style="cellStyle"
          border
          @cell-mouse-enter="clickhandle"
          style="width: 95%">
          <el-table-column prop="date" min-width="110"> </el-table-column>
          <el-table-column
            v-for="(item, index) in room"
            :prop="item.roomName"
            :key="index"
            :label="item.roomName"
            width="100">
            <!-- 预约详情弹出框 -->
            <template slot-scope="scope">
              <el-popover transition="el-zoom-in-center"	 trigger="hover" placement="left">
                <el-form ref="form" :model="details" label-width="100px">
                  <el-form-item label="使用单位">
                    <el-input v-model="details.department" readonly></el-input>
                  </el-form-item>
                  <el-form-item label="预约人">
                    <el-input v-model="details.roomUser" readonly></el-input>
                  </el-form-item>
                  <el-form-item label="隶属部门">
                    <el-input v-model="details.userFrom" readonly></el-input>
                  </el-form-item>
                  <el-form-item label="会议室名称">
                    <el-input v-model="details.roomName" readonly></el-input>
                  </el-form-item>
                  <el-form-item label="设备">
                    <el-input v-model="details.equipment" readonly></el-input>
                  </el-form-item>
                  <el-form-item label="联系方式">
                    <el-input v-model="details.userPhone" readonly></el-input>
                  </el-form-item>
                  <el-form-item label="参会人数">
                    <el-input v-model="details.userNum" readonly></el-input>
                  </el-form-item>
                  <el-form-item label="参会人员">
                    <el-input v-model="details.users" readonly></el-input>
                  </el-form-item>
                  <el-form-item label="会议主题">
                    <el-input v-model="details.meetingTheme" readonly></el-input>
                  </el-form-item>
                  <el-form-item label="会议时间">
                    <el-input v-model="details.time" readonly></el-input>
                  </el-form-item>
                  <el-form-item label="备注">
                    <template>
                      <el-form-item v-if="details.remark==null">
                        <el-input value="无" readonly></el-input>
                      </el-form-item>
                      <el-form-item v-else>
                        <el-input v-model="details.remark" readonly></el-input>
                      </el-form-item>
                    </template>
                  </el-form-item>
                </el-form>
                <div   slot="reference">
                  <el-button
                    type="text"
                    style="
                      float: left;
                      font-size: 18px;
                      margin-left: 10%;
                      color: white;
                      line-height: 25px;
                    "
                    >{{ item.capacity }}</el-button
                  >
                  <el-button
                    type="text"
                    :class="
                      item.equipment != '无'
                        ? 'iconfont icon-shexiangtou'
                        : 'iconfont icon-shexiangtou_guanbi'
                    "
                    style="
                      float: right;
                      font-size: 25px;
                      margin-right: 10%;
                      color: #686868;
                    "
                  ></el-button>
                </div>
              </el-popover>
            </template>
            <!-- <span style="float:left" class="iconfont icon-mic"></span>
            <span  style="float:right" class="iconfonticon-shexiangtou_guanbi"></span>-->
          </el-table-column>
        </el-table>
      </el-col>
      <!-- <el-col :span="8">
        
      </el-col> -->
    </el-row>
  </div>
</template>

  <script>
export default {
  created() {
    this.getNowTime();
  },
  mounted() {
    this.getTanleMsg();
  },
  methods: {
    getNowTime() {
      var now = new Date();
      var year = now.getFullYear(); //得到年份
      var month = now.getMonth(); //得到月份
      var date = now.getDate(); //得到日期
      month = month + 1;
      month = month.toString().padStart(2, "0");
      date = date.toString().padStart(2, "0");
      var defaultDate = `${year}-${month}-${date}`;
      // var defaultDate = '2020-09-24';
      this.datevalue = defaultDate;
    },
    getTanleMsg() {
      this.$http({
        url: this.$http.adornUrl("/meeting/meet/table"),
        method: "post",
        data: {
          value: this.datevalue,
        },
        // 设置请求头
        headers: {
          "Content-Type": "application/json",
        },
      }).then(({ data }) => {
        if (data && data.code === 0) {
          console.log(data);
          this.tableData = data.table;
          // this.choosetable = data.choosetable;
          this.room = data.room;
          // this.tableData = data.list;

          // console.log();
          this.formcache = {
            department: data.room[1].roomArea,
            name: data.now_user.thename,
            mobile: data.now_user.mobile,
            belong: data.now_user.department,
            datechoose: this.datevalue,
            theme: [],
            date1: null,
            date2: null,
            room: null,
          };
          this.form = this.formcache;
        } else {
          this.$message.error(data.msg);
        }
      });
    },
    submitForm(form) {
      this.$refs[form].validate((valid) => {
        if (valid) {
          console.log("submit!");
          this.$http({
            url: this.$http.adornUrl("/generator/servicemeeting/formsubmit"),
            method: "post",
            data: {
              datechoose: this.form.datechoose,
              date1: this.form.date1,
              date2: this.form.date2,
              datechoose: this.form.datechoose,
              department: this.form.department,
              leader: this.form.leader,
              mobile: this.form.mobile,
              name: this.form.name,
              note: this.form.note,
              room: this.form.room,
              sum: this.form.sum,
              theme: this.form.theme,
            },
            // 设置请求头
            headers: {
              "Content-Type": "application/json",
            },
          }).then(({ data }) => {
            if (data && data.code === 0) {
              // console.log(data);
              // console.log(form);
              // console.log(this.form);
              this.$message(data.msg);
              this.$router.replace({ path: "/generator-user_servicemeeting" });
            } else {
              this.$message.error(data.msg);
            }
          });
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    selectedChange(val) {
      console.log(val);
      for (let i = 0; i < val.length; i++) {
        console.log(i);
        console.log(val[i]);
        if (val[i] == "其他（建议备注中说明）") {
          if (i != 0) {
            this.form.theme = ["其他（建议备注中说明）"];
            break;
          } else {
            val.splice(i, 1);
          }
        }
      }
    },
    context() {
      console.log("当前各项值");
      console.log("this.timesign");
      console.log(this.timesign);
      console.log("this.timestart");
      console.log(this.timestart);
      console.log("this.timeend");
      console.log(this.timeend);
      console.log("this.roomsign");
      console.log(this.roomsign);
      console.log("this.bechosed");
      console.log(this.bechosed);
    },

    resetchose() {
      this.timesign = false;
      this.timestart = "";
      this.timeend = "";
      this.roomsign = "";
      this.bechosed = false;
    },
    reset() {
      console.log("重置事件");
      resetchose();
      this.form = this.formcache;
      // this.$router.go(0);
    },
    // 单元格的 style 的回调方法
    cellStyle({ row, column, rowIndex, columnIndex }) {
      //初始渲染已选择
       try {
        if (typeof row[column.label]["id"] != "undefined") {
          if (row[column.label]["startTime"] === row["date"].split(":")[0])
            return "border-radius: 8px;background-color:#909399;color:white;padding:0;border-top: 2px solid #475364";
          else if (
            row[column.label]["endTime"] ===
            row["date"].split(":")[1].split("-")[1]
          )
            return "border-radius: 8px;background-color:#909399;color:white;padding:0;border-bottom: 2px solid #475364";
          else
            return "border-radius: 8px;background-color:#909399;color:white;padding:0";
        }
      } catch (error) {}

      //点击选择
      // if (columnIndex != 0 && this.timesign == true) {
      //   if (
      //     this.timeend != "" &&
      //     column.label == this.roomsign &&
      //     rowIndex >= Number(this.timestart - 7) &&
      //     rowIndex <= Number(this.timeend - 8) &&
      //     this.bechosed == true
      //   ) {
      //     //跨区域选择
      //     return "border-radius: 8px;background-color:#3E8EF7;color:white;padding:0";
      //   }

      //   if (
      //     column.label == this.roomsign &&
      //     rowIndex == Number(this.timestart - 7)
      //   ) {
      //     //单选
      //     return "border-radius: 8px;background-color:#3E8EF7;color:white;padding:0";
      //   }
      // }

      if (columnIndex != 0)
        return "border-radius: 8px;background-color:rgb(33, 185, 251);padding:0;pointer-events: none";
    },
    // close () {
      
    //   this.details = {};
    // },
    clickhandle(row, column, event, cell) {
      // 获取选择的会议室
      // let chooseroom;
      // for (let i = 0; i < this.room.length; i++)
      //   if (this.room[i].roomName == column.label) chooseroom = this.room[i];
      console.log("row[column.label]");
      console.log(row[column.label]);
      
      this.details = {};
      if (typeof row[column.label]["id"] != "undefined") {
        this.details = {
          department: row[column.label].department,
          roomUser: row[column.label].roomUser,
          userNum: row[column.label].userNum,
          userFrom: row[column.label].userFrom,
          roomName: row[column.label].roomName,
          equipment: row[column.label].equipment,
          userPhone: row[column.label].userPhone,
          meetingTheme: row[column.label].meetingTheme,
          users: row[column.label].users,
          remark: row[column.label].remark,
          time: row[column.label].startTime + ":00-" + row[column.label].endTime + ":00",       
        };
        console.log("详情");
        console.log(this.details);
      } else {
        this.visible = false;
      }

      // for(let i = 0; i < b.length; i++){
      //   let time1 = b[i].item.startTime.split(' ');//开始时间
      //   let time2 = b[i].item.endTime.split(' ');//结束时间
      //   if(
      //     b[i].item.roomName == column.label &&
      //     time1[0] == this.datevalue){
      //     if(
      //       a[0].split(":")[0] >= Number(time1[1].split(':')[0]) && 
      //       a[1].split(":")[0] <= Number(time2[1].split(':')[0])
      //     ){ 
      //       console.log(a[0].split(":")[0]);
      //       console.log(Number(time1[1].split(':')[0]));
      //       console.log(a[1].split(":")[0]);
      //       console.log(Number(time2[1].split(':')[0]));
      //       this.details = {
      //         department: b[i].item.department,
      //         headCount: b[i].item.headCount,
      //         leader: b[i].item.leader,
      //         meetingTheme: b[i].item.meetingTheme,
      //         remark: b[i].item.remark,
      //         roomName: b[i].item.roomName,
      //         roomUser: b[i].item.roomUser,
      //         startTime: b[i].item.startTime,
      //         endTime: b[i].item.endTime,
      //         remark: b[i].item.remark,
      //         mobile: b[i].mobile,
      //       };
      //       this.visible = true;
      //       console.log("我进来了");
      //       console.log(this.details);
      //     }
      //   }
      
      // console.log("点击事件");
      // console.log(row[column.label]);
      // console.log("行");
      // console.log(row);
      // console.log("列");
      // console.log(column);
      // console.log("====");
      // 获取选择的会议室人数判断上限
      // this.roomsize = chooseroom.capacity;

      // if (this.timesign == false) {
      //   //第一次点击
      //   if (typeof row[column.label]["id"] != "undefined") {
      //     this.$message.error("当前时间段已被预约");
      //     this.resetchose();
      //   } else {
      //     this.form.room = column.label;
      //     this.roomsign = column.label;
      //     this.form.date1 = a[0];
      //     this.timestart = a[0].split(":")[0];
      //     this.timeend = "";
      //     this.form.date2 = a[1];
      //     this.timesign = true;
      //   }
      // } else {
      //   //第二次点击
      //   if (this.form.room == column.label) {
      //     //仍选择该会议室
      //     let tstart = Number(this.timestart);
      //     let tend = Number(a[0].split(":")[0]);

      //     //判断选择时间段内部有无已选
      //     for (let i = tstart; i <= tend; i++) {
      //       if (
      //         typeof this.tableData[i - 7][column.label]["id"] != "undefined"
      //       ) {
      //         this.$message.error("当前时间段已有被预约时间段");
      //         this.resetchose();
      //         break;
      //       }
      //     }

      //     if (Number(a[0].split(":")[0]) <= Number(this.timestart)) {
      //       //判断第二次时间是否在第一次前面
      //       this.$message.error("请选择正确的时间段");
      //       this.resetchose();
      //     } else {
      //       this.form.date2 = a[1];
      //       this.timeend = a[1].split(":")[0];
      //       this.bechosed = true;
      //     }
      //   } else {
      //     this.$message.error("请选择同一会议室进行预约");
      //     this.resetchose();
      //   }
      // }
    }
    // addIconClass({ row, column, rowIndex, columnIndex }) {
    //  if (columnIndex != 0)
    //       return "iconfont icon-mic icon-shexiangtou_guanbi";

    // },

    // 表头行的 style 的回调方法
    // headCellStyle({ row, column, rowIndex, columnIndex }) {
    //     if (columnIndex != 0 && rowIndex === 0) {
    //         return `padding-left:40px;`;
    //     }
    // },
  },

  data() {
    var validateSum = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请填写参会人数"));
      } else if (value > this.roomsize) {
        callback(new Error("参会人数超出上限"));
      } else {
        callback();
      }
    };
    return {
      tableData: [],
      room: [],
      roomsize: "",
      form: {},
      
      formcache: {},
      datevalue: "",
      timestart: "",
      datasign: [],
      choosetable: {},
      details: {},//获取详情信息
      timesign: false, //第几次点击
      timestart: "",
      timeend: "",
      roomsign: "", //标记点击选择的会议室
      bechosed: false,
      visible: false,//详情弹出框是否显示
      rules: {
        room: [{ required: true, message: "请填写会议室", trigger: "change" }],
        sum: [{ validator: validateSum, trigger: "blur" }],
        leader: [
          { required: true, message: "请填写参会人员", trigger: "blur" },
        ],
        theme: [
          {
            type: "array",
            required: true,
            message: "请至少选择一个活动性质",
            trigger: "change",
          },
        ],
      },
      // expireTimeOption: {
      //   disabledDate(date) {
      //     // 当天可选：
      //     return date.getTime() < Date.now() - 24 * 60 * 60 * 1000;
      //   },
      // },
    };
  },
};
</script>


<style>
.el-table--border,
.el-table--group {
  border: none;
}
/* // 表格最外层边框-底部边框 */
.el-table--border::after,
.el-table--group::after {
  width: 0;
}
.customer-table::before {
  width: 0;
}
.customer-table .el-table__fixed-right::before,
.el-table__fixed::before {
  width: 0;
}
</style>
<style>
/* inpu滚动条 */
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: textfield;
}
input[type="number"] {
  -moz-appearance: textfield;
}
</style>