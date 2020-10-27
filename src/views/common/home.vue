

<template>
  <div>
    <el-row>
      <el-col :span="16">
        <span style="text-align: left; font-weight: 600; font-size: 14px"
          >选择日期:</span
        >
        <el-date-picker
          v-model="datevalue"
          type="date"
          @change="getTanleMsg"
          placeholder="选择日期"
          value-format="yyyy-MM-dd"
          :picker-options="expireTimeOption"
        >
        </el-date-picker>
        <el-table
          class="customer-table"
          :data="tableData"
          :cell-style="cellStyle"
          border
          @cell-click="clickhandle"
          style="width: 95%"
        >
          <el-table-column prop="date" min-width="110"> </el-table-column>
          <el-table-column
            v-for="(item, index) in room"
            :prop="item.roomName"
            :key="index"
            :label="item.roomName"
            width="100"
          >
            <template slot-scope="scope">
              <!-- <el-popover trigger="hover" placement="right">
                <el-form ref="form" :model="details" label-width="100px">
                  <el-form-item label="会议室名称">
                    <el-input v-model="details.roomName" readonly></el-input>
                  </el-form-item>
                  <el-form-item label="所属区域">
                    <el-input v-model="details.roomArea" readonly></el-input>
                  </el-form-item>
                  <el-form-item label="会议室地点">
                    <el-input v-model="details.location" readonly></el-input>
                  </el-form-item>
                  <el-form-item label="设备">
                    <el-input v-model="details.equipment" readonly></el-input>
                  </el-form-item>
                  <el-form-item label="容纳人数">
                    <el-input v-model="details.capacity" readonly></el-input>
                  </el-form-item>
                </el-form>
               </el-popover> -->
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
            </template>
            <!-- <span style="float:left" class="iconfont icon-mic"></span>
            <span  style="float:right" class="iconfonticon-shexiangtou_guanbi"></span>-->
          </el-table-column>
        </el-table>
      </el-col>
      <el-col :span="8">
        <el-form ref="form" :rules="rules" :model="form" label-width="80px">
          <el-form-item
            label="提交会议室预约申请"
            label-width="180px"
            style="text-align: left; font-weight: 600; font-size: 14px"
          >
          </el-form-item>
          <el-form-item label="使用单位" prop="department">
            <el-input v-model="form.department"></el-input>
          </el-form-item>

          <el-col :span="12">
            <el-form-item label="联系人">
              <el-input v-model="form.name" readonly></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="隶属单位">
              <el-input readonly v-model="form.belong"></el-input>
            </el-form-item>
          </el-col>

          <el-form-item label="联系电话">
            <el-input
              type="number"
              readonly
              v-model="form.mobile"
              @mousewheel.native.prevent
            ></el-input>
          </el-form-item>
          <el-form-item
            label="拥有设备"
            size="mini"
            prop="equipment"
            style="pointer-events: none"
          >
            <el-checkbox-group v-model="form.equipment">
              <el-checkbox v-for="eq in eqList" :key="eq.id" :label="eq.name">{{
                eq.name
              }}</el-checkbox>
            </el-checkbox-group>
          </el-form-item>
          <el-form-item label="会议室" prop="room">
            <el-input readonly v-model="form.room"></el-input>
          </el-form-item>
          <el-form-item label="活动日期">
            <el-col :span="11">
              <el-input v-model="form.datechoose"></el-input>
            </el-col>
          </el-form-item>
          <el-form-item label="活动时间">
            <el-col :span="11">
              <el-input
                readonly
                placeholder="开始时间"
                v-model="form.date1"
                style="width: 100%"
              ></el-input>
            </el-col>
            <el-col class="line" :span="2" style="text-align: center">-</el-col>
            <el-col :span="11">
              <el-input
                readonly
                placeholder="结束时间"
                v-model="form.date2"
                style="width: 100%"
              ></el-input>
            </el-col>
          </el-form-item>
          <el-form-item label="参会人数" prop="sum">
            <el-col :span="11">
              <el-input
                type="number"
                min="2"
                max="100"
                v-model="form.sum"
              ></el-input>
            </el-col>
          </el-form-item>
          <el-form-item label="参会人员" prop="leader">
            <el-input v-model="form.leader"></el-input>
          </el-form-item>

          <el-form-item label="会议用途" prop="theme">
            <el-checkbox-group v-model="form.theme" @change="selectedChange">
              <el-checkbox label="研讨会" name="type"></el-checkbox>
              <el-checkbox label="接待" name="type"></el-checkbox>
              <el-checkbox label="讲座" name="type"></el-checkbox>
              <el-checkbox
                label="其他（建议备注中说明）"
                name="type"
              ></el-checkbox>
            </el-checkbox-group>
          </el-form-item>

          <el-form-item label="备注">
            <el-input type="textarea" v-model="form.note"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="submitForm('form')"
              >立即预约</el-button
            >
            <el-button @click="reset">重置</el-button>
          </el-form-item>
        </el-form>
      </el-col>
    </el-row>
  </div>
</template>

  <script>
export default {
  created() {
    this.getNowTime();
    this.geteqlist();
  },
  mounted() {
    this.getTanleMsg();
  },
  methods: {
    //获取会议室详情信息
    // getDetails(row, column, cell, event) {
    //   this.details = {};
    //   for (let i = 0; i < this.room.length; i++) {
    //     if (this.room[i].roomName == column.label) {
    //       this.details = this.room[i];
    //     }
    //   }
    // },
    getNowTime() {
      var now = new Date();
      let hourchecksign = this.datevalue;
      var hour = now.getHours(); //得到小时
      // if (hour >= 20) {
      //   this.hourvalue = 0;
      //   hourchecksign = 1;
      //   now = new Date(now.getTime() + 24 * 60 * 60 * 1000);
      // }
      var year = now.getFullYear(); //得到年份
      var month = now.getMonth(); //得到月份
      var date = now.getDate(); //得到日期
      month = month + 1;
      month = month.toString().padStart(2, "0");
      date = date.toString().padStart(2, "0");
      var defaultDate = `${year}-${month}-${date}`;
      if (this.datevalue != "") {
        //非初次加载
        if (this.datevalue === defaultDate) {
          // var defaultDate = '2020-09-24';
          //是今天
          this.hourvalue = hour;
        } else {
          //不是
          this.hourvalue = 0;
        }
      } else {
        this.datevalue = defaultDate;
        this.hourvalue = hour;
      }

      this.expireTimeOption = {
        disabledDate(date) {
          // 当天可选：
          return date.getTime() < now.getTime() - 24 * 60 * 60 * 1000;
        },
      };
    },
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
    getTanleMsg() {
      this.reset();
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
          console.log("data");
          console.log(data);
          this.tableData = data.table;
          this.room = data.room;
          this.now_user = data.now_user;
          this.formcache = {
            department: null,
            name: data.now_user.thename,
            mobile: data.now_user.mobile,
            belong: data.now_user.department,
            datechoose: this.datevalue,
            theme: [],
            date1: null,
            date2: null,
            room: null,
            equipment: [],
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

          if (this.form.equipment.length == 0) this.form.equipment = ["无"];
          let str = this.form.theme[0];
          if (str.search("其他") != -1) this.form.theme[0] = "其他";
          this.$http({
            url: this.$http.adornUrl("/meeting/meet/submit"),
            method: "post",
            data: {
              datechoose: this.form.datechoose,
              date1: this.form.date1,
              date2: this.form.date2,
              datechoose: this.form.datechoose,
              department: this.form.department,
              from: this.form.belong,
              equipment: this.form.equipment,
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
              this.$router.replace({ path: "/meeting-meet" });
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
      // console.log(val);
      for (let i = 0; i < val.length; i++) {
        // console.log(i);
        // console.log(val[i]);
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
      this.form.equipment = [];
    },
    reset() {
      this.formcache = {
        department: null,
        name: this.now_user.thename,
        mobile: this.now_user.mobile,
        belong: this.now_user.department,
        datechoose: this.datevalue,
        theme: [],
        date1: null,
        date2: null,
        room: null,
        equipment: [],
      };
      this.form = this.formcache;
      this.resetchose();
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
      if (columnIndex != 0 && this.timesign == true) {
        if (
          this.timeend != "" &&
          column.label == this.roomsign &&
          rowIndex >= Number(this.timestart - 7) &&
          rowIndex <= Number(this.timeend - 8) &&
          this.bechosed == true
        ) {
          //跨区域选择
          return "border-radius: 8px;background-color:#3E8EF7;color:white;padding:0";
        }

        if (
          column.label == this.roomsign &&
          rowIndex == Number(this.timestart - 7)
        ) {
          //单选
          return "border-radius: 8px;background-color:#3E8EF7;color:white;padding:0";
        }
      }

      if (columnIndex != 0)
        return "border-radius: 8px;background-color:rgb(33, 185, 251);padding:0";
    },
    clickhandle(row, column, event, cell) {
      this.getNowTime();
      if (column.property != "date") {
        let a = row.date.split("-");
        // console.log("点击事件");
        // console.log(row[column.label]);
        console.log("行");
        console.log(row);
        console.log("列");
        console.log(column);
        // console.log("====");

        // 获取选择的会议室
        let chooseroom;
        for (let i = 0; i < this.room.length; i++)
          if (this.room[i].roomName == column.label) chooseroom = this.room[i];

        // 获取选择的会议室人数判断上限
        this.roomsize = chooseroom.capacity;

        if (this.timesign == false) {
          //第一次点击
          console.log("row[column.label]");
          console.log(row[column.label]);
          if (typeof row[column.label]["id"] != "undefined") {
            this.$message.error("当前时间段已被预约");
            this.resetchose();
          } else if (Number(row.date.split(":")[0]) <= Number(this.hourvalue)) {
            this.$message.error("当前时间段已过，无法预约");
            this.resetchose();
          } else {
            this.form.room = column.label;

            let eq = row[column.label]["equipment"];
            if (eq != "无") {
              this.form.equipment = eq.split(",");
            } else {
              this.form.equipment = [];
            }

            this.roomsign = column.label;
            this.form.date1 = a[0];
            this.timestart = a[0].split(":")[0];
            this.timeend = "";
            this.form.date2 = a[1];
            this.timesign = true;
          }
        } else {
          //第二次点击
          if (this.form.room == column.label) {
            //仍选择该会议室
            let tstart = Number(this.timestart);
            let tend = Number(a[0].split(":")[0]);

            //判断选择时间段内部有无已选
            for (let i = tstart; i <= tend; i++) {
              if (
                typeof this.tableData[i - 7][column.label]["id"] != "undefined"
              ) {
                this.$message.error("当前时间段已有被预约时间段");
                this.resetchose();
                break;
              }
            }

            if (Number(a[0].split(":")[0]) <= Number(this.timestart)) {
              //判断第二次时间是否在第一次前面
              this.$message.error("请选择正确的时间段");
              this.resetchose();
            } else {
              this.form.date2 = a[1];
              this.timeend = a[1].split(":")[0];
              this.bechosed = true;
            }
          } else {
            this.$message.error("请选择同一会议室进行预约");
            this.resetchose();
          }
        }
      }
    },
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
      now_user: {},
      eqList: [],
      roomsize: "",
      // details: {},
      form: {},
      formcache: {},
      datevalue: "",
      hourvalue: "",
      timestart: "",
      datasign: [],
      choosetable: {},
      timesign: false, //第几次点击
      timestart: "",
      timeend: "",
      roomsign: "", //标记点击选择的会议室
      bechosed: false,
      rules: {
        department: [
          { required: true, message: "请填写使用单位", trigger: "change" },
        ],
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
      expireTimeOption: {},
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