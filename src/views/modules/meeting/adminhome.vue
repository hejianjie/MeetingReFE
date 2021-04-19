<template>
  <div>

    <el-row v-loading="dataListLoading" element-loading-text="拼命加载中">

      <el-col :span="16">
        <el-date-picker
          v-model="datevalue"
          type="date"
          @change="getTanleMsg"
          placeholder="选择日期"
          value-format="yyyy-MM-dd"
        >
        </el-date-picker>
        <el-table
          class="customer-table"
          :data="tableData"
          :cell-style="cellStyle"
          :cell-class-name="cellClass"
          border
          @cell-mouse-enter="clickhandle"
          style="width: 95%"
        >

          <el-table-column prop="date" min-width="110"> </el-table-column>

          <el-table-column
            v-for="(item, index) in room"
            :prop="item.roomName"
            :key="index"

            :label="item.roomName + '(容纳' + item.capacity + '人)'"

            width="100"
          >
            <!-- 预约详情弹出框 -->
            <template slot-scope="scope">
              <el-popover
                transition="el-zoom-in-center"
                trigger="hover"
                placement="left"
              >

                <el-form ref="form" :model="details" label-width="100px">

                  <el-form-item label="使用单位">
                    <el-input
                      v-model="details.department"
                      readonly
                    ></el-input>
                  </el-form-item>
                  <el-form-item label="预约人">
                    <el-input
                      v-model="details.roomUser"
                      readonly
                    ></el-input>
                  </el-form-item>
                  <el-form-item label="隶属部门">
                    <el-input
                      v-model="details.userFrom"
                      readonly
                    ></el-input>
                  </el-form-item>
                  <el-form-item label="会议室名称">
                    <el-input
                      v-model="details.roomName"
                      readonly
                    ></el-input>
                  </el-form-item>
                  <el-form-item label="设备">
                    <el-input
                      v-model="details.equipment"
                      readonly
                    ></el-input>
                  </el-form-item>
                  <el-form-item label="联系方式">
                    <el-input
                      v-model="details.userPhone"
                      readonly
                    ></el-input>
                  </el-form-item>
                  <el-form-item label="参会人数">
                    <el-input
                      v-model="details.userNum"
                      readonly
                    ></el-input>

                  </el-form-item>
                  <el-form-item label="会议主题">
                    <el-input
                      v-model="details.meetingTheme"
                      readonly
                    ></el-input>
                  </el-form-item>
                  <el-form-item label="会议时间">
                    <el-input
                      v-model="details.time"
                      readonly
                    ></el-input>
                  </el-form-item>
                  <el-form-item label="备注">
                    <template>

                      <el-form-item v-if="details.remark==null">
                        <el-input
                          value="无"
                          readonly
                        ></el-input>
                      </el-form-item>
                      <el-form-item v-else>
                        <el-input
                          v-model="details.remark"
                          readonly
                        ></el-input>
                      </el-form-item>
                    </template>
                  </el-form-item>
                </el-form>
                <div slot="reference">

                  {{
                    tableData[scope.$index][item.roomName].status == null
                      ? "空闲"
                      : "占用"
                  }}

                </div>
              </el-popover>
            </template>
          </el-table-column>
        </el-table>
        <el-col :span="13">
          <!-- <el-card shadow="hover">
            上方单元格内的数字表示该会议室的可容纳人数，图标表示该会议室是否拥有设备。
          </el-card> -->
        </el-col>
      </el-col>
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
      this.datevalue = defaultDate;
    },
    getTanleMsg() {
      this.dataListLoading = true;
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
          this.tableData = data.table;
          // this.choosetable = data.choosetable;
          this.room = data.room;
          // this.tableData = data.list;
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
        this.dataListLoading = false;
      });
    },
    cellClass({ row, column, rowIndex, columnIndex }) {
      return "celltextcenter";
    },
    // 单元格的 style 的回调方法
    cellStyle({ row, column, rowIndex, columnIndex }) {
      //初始渲染已选择
      try {

        if (typeof row[column.label.split('(')[0]]["id"] != "undefined") {
          if (row[column.label.split('(')[0]]["startTime"] === row["date"].split(":")[0])

            return "border-radius: 8px;background-color:#D3D3D3;color:white;padding:0;border-top: 2px solid #475364";
          else if (
            row[column.label.split('(')[0]]["endTime"] ===
            row["date"].split(":")[1].split("-")[1]
          )
            return "border-radius: 8px;background-color:#D3D3D3;color:white;padding:0;border-bottom: 2px solid #475364";
          else
            return "border-radius: 8px;background-color:#D3D3D3;color:white;padding:0";
        }
      } catch (error) {}

      if (columnIndex != 0)
        return "border-radius: 8px;background-color:#FFFFFF;padding:0;pointer-events: none";
    },
    // close () {

    //   this.details = {};
    // },
    clickhandle(row, column, event, cell) {
      // 获取选择的会议室
      // let chooseroom;
      // for (let i = 0; i < this.room.length; i++)

      //   if (this.room[i].roomName == column.label.split('(')[0]) chooseroom = this.room[i];

      this.details = {};
      if (typeof row[column.label.split('(')[0]]["id"] != "undefined") {
        this.details = {

          department: row[column.label.split('(')[0]].department,
          roomUser: row[column.label.split('(')[0]].roomUser,
          userNum: row[column.label.split('(')[0]].userNum,
          userFrom: row[column.label.split('(')[0]].userFrom,
          roomName: row[column.label.split('(')[0]].roomName,
          equipment: row[column.label.split('(')[0]].equipment,
          userPhone: row[column.label.split('(')[0]].userPhone,
          meetingTheme: row[column.label.split('(')[0]].meetingTheme,
          users: row[column.label.split('(')[0]].users,
          remark: row[column.label.split('(')[0]].remark,
          time:
            row[column.label.split('(')[0]].startTime +
            ":00-" +
            row[column.label.split('(')[0]].endTime +

            ":00",
        };
      } else {
        this.visible = false;
      }
    },
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
      dataListLoading: false,
      formcache: {},
      datevalue: "",
      timestart: "",
      datasign: [],
      choosetable: {},
      details: {}, //获取详情信息
      timesign: false, //第几次点击
      timestart: "",
      timeend: "",
      roomsign: "", //标记点击选择的会议室
      bechosed: false,
      visible: false, //详情弹出框是否显示
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
.celltextcenter {
  text-align: center;
}
</style>