<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="职业" prop="voctName">
      <el-input v-model="dataForm.voctName" placeholder="职业"></el-input>
    </el-form-item>
    <el-form-item label="" prop="deptId">
      <el-input v-model="dataForm.deptId" placeholder=""></el-input>
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
          voctId: 0,
          voctName: '',
          deptId: ''
        },
        dataRule: {
          voctName: [
            { required: true, message: '职业不能为空', trigger: 'blur' }
          ],
          deptId: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.voctId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.voctId) {
            this.$http({
              url: this.$http.adornUrl(`/generator/voct/info/${this.dataForm.voctId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.voctName = data.voct.voctName
                this.dataForm.deptId = data.voct.deptId
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
              url: this.$http.adornUrl(`/generator/voct/${!this.dataForm.voctId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'voctId': this.dataForm.voctId || undefined,
                'voctName': this.dataForm.voctName,
                'deptId': this.dataForm.deptId
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
