<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="设备名称" prop="name">
      <el-input v-model="dataForm.name" placeholder="设备名称"></el-input>
    </el-form-item>
    <el-form-item label="设备信息" prop="message">
      <el-input v-model="dataForm.message" placeholder="设备信息"></el-input>
    </el-form-item>
    <el-form-item label="预留字段1" prop="data1">
      <el-input v-model="dataForm.data1" placeholder="预留字段1"></el-input>
    </el-form-item>
    <el-form-item label="预留字段2" prop="data2">
      <el-input v-model="dataForm.data2" placeholder="预留字段2"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          name: '',
          message: '',
          data1: '',
          data2: ''
        },
        dataRule: {
          name: [
            { required: true, message: '设备名称不能为空', trigger: 'blur' }
          ],
          message: [
            { required: true, message: '设备信息不能为空', trigger: 'blur' }
          ],
          data1: [
            { required: true, message: '预留字段1不能为空', trigger: 'blur' }
          ],
          data2: [
            { required: true, message: '预留字段2不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/meeting/meetroomequipment/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.name = data.meetRoomEquipment.name
                this.dataForm.message = data.meetRoomEquipment.message
                this.dataForm.data1 = data.meetRoomEquipment.data1
                this.dataForm.data2 = data.meetRoomEquipment.data2
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/meeting/meetroomequipment/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'name': this.dataForm.name,
                'message': this.dataForm.message,
                'data1': this.dataForm.data1,
                'data2': this.dataForm.data2
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
