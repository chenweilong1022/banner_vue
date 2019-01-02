<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户手机号" prop="phone">
      <el-input v-model="dataForm.phone" placeholder="用户手机号"></el-input>
    </el-form-item>
    <el-form-item label="用户创建时间" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="用户创建时间"></el-input>
    </el-form-item>
    <el-form-item label="平台ios|安卓" prop="platform">
      <el-input v-model="dataForm.platform" placeholder="平台ios|安卓"></el-input>
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
          phone: '',
          createTime: '',
          platform: ''
        },
        dataRule: {
          phone: [
            { required: true, message: '用户手机号不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '用户创建时间不能为空', trigger: 'blur' }
          ],
          platform: [
            { required: true, message: '平台ios|安卓不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/generator/bananauser/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.phone = data.bananauser.phone
                this.dataForm.createTime = data.bananauser.createTime
                this.dataForm.platform = data.bananauser.platform
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
              url: this.$http.adornUrl(`/generator/bananauser/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'phone': this.dataForm.phone,
                'createTime': this.dataForm.createTime,
                'platform': this.dataForm.platform
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
