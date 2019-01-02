<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="商品id" prop="goodsId">
        <el-select v-model="dataForm.goodsId" placeholder="请选择">
          <el-option
            v-for="item in options"
            :key="item.goddsid"
            :label="item.title"
            :value="item.goddsid">
          </el-option>
        </el-select>
    </el-form-item>
    <el-form-item label="卡密" prop="camilo">
      <el-input v-model="dataForm.camilo" placeholder="卡密"></el-input>
    </el-form-item>
   <!-- <el-form-item label="状态0:未发放1:已发放" prop="status">
      <el-input v-model="dataForm.status" placeholder="状态0:未发放1:已发放"></el-input>
    </el-form-item>
    <el-form-item label="商品名称" prop="goodsName">
      <el-input v-model="dataForm.goodsName" placeholder="商品名称"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>
    </el-form-item>-->
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
        options: [],
        dataForm: {
          id: 0,
          goodsId: '',
          camilo: ''
        },
        dataRule: {
          goodsId: [
            { required: true, message: '商品id不能为空', trigger: 'blur' }
          ],
          camilo: [
            { required: true, message: '卡密不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.goodsAll()
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/generator/bananagoodscamilo/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.goodsId = data.bananaGoodsCamilo.goodsId
                this.dataForm.camilo = data.bananaGoodsCamilo.camilo
              }
            })
          }
        })
      },
      goodsAll () {
        this.$http({
          url: this.$http.adornUrl(`/generator/bananagoods/listAll`),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.options = data.data
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/generator/bananagoodscamilo/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'goodsId': this.dataForm.goodsId,
                'camilo': this.dataForm.camilo
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
