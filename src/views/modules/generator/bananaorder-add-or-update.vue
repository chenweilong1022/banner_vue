<template>
  <el-dialog
    :title="!dataForm.id ? '详情' : '详情'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="订单商品id" prop="goodsid">
      <el-input v-model="dataForm.goodsid" placeholder="订单商品id"></el-input>
    </el-form-item>
    <el-form-item label="商品名称" prop="title">
      <el-input v-model="dataForm.title" placeholder="商品名称"></el-input>
    </el-form-item>
    <el-form-item label="购买数量" prop="count">
      <el-input v-model="dataForm.count" placeholder="购买数量"></el-input>
    </el-form-item>
    <el-form-item label="购买价钱" prop="price">
      <el-input v-model="dataForm.price" placeholder="购买价钱"></el-input>
    </el-form-item>
    <el-form-item label="商品总价格" prop="totalPrice">
      <el-input v-model="dataForm.totalPrice" placeholder="商品总价格"></el-input>
    </el-form-item>
    <el-form-item label="支付code" prop="code">
      <el-input v-model="dataForm.code" placeholder="支付code"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>
    </el-form-item>
    <el-form-item label="支付时间" prop="paytime">
      <el-input v-model="dataForm.paytime" placeholder="支付时间"></el-input>
    </el-form-item>
    <el-form-item label="支付方式" prop="payType">
      <el-input v-model="dataForm.payType" placeholder="支付方式1:微信2:支付宝"></el-input>
    </el-form-item>
    <el-form-item label="充值账号" prop="account">
      <el-input v-model="dataForm.account" placeholder="充值账号"></el-input>
    </el-form-item>
    <el-form-item label="是否已经充值" prop="status">
      <el-input v-model="dataForm.status" placeholder="是否已经充值0:未充值 1:已经充值"></el-input>
    </el-form-item>
    <el-form-item label="充值方式" prop="topUpWay">
      <el-input v-model="dataForm.topUpWay" placeholder="该商品充值方式1:卡密2:自动充值3:手动充值"></el-input>
    </el-form-item>
    <el-form-item label="卡密" prop="password">
      <el-input v-model="dataForm.password" placeholder="卡密"></el-input>
    </el-form-item>
    <el-form-item label="充值官方网站" prop="url">
      <el-input v-model="dataForm.url" placeholder="充值官方网站"></el-input>
    </el-form-item>
    <el-form-item label="是否支付" prop="isPay">
      <el-input v-model="dataForm.isPay" placeholder="是否支付0未支付1已支付"></el-input>
    </el-form-item>
    <el-form-item label="用户id" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="用户id"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">关闭</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          orderid: 0,
          goodsid: '',
          title: '',
          pic: '',
          count: '',
          price: '',
          totalPrice: '',
          code: '',
          createTime: '',
          paytime: '',
          payType: '',
          account: '',
          status: '',
          topUpWay: '',
          password: '',
          url: '',
          isPay: '',
          userId: ''
        },
        dataRule: {
          goodsid: [
            { required: true, message: '订单商品id不能为空', trigger: 'blur' }
          ],
          title: [
            { required: true, message: '商品名称不能为空', trigger: 'blur' }
          ],
          pic: [
            { required: true, message: '商品图片不能为空', trigger: 'blur' }
          ],
          count: [
            { required: true, message: '购买数量不能为空', trigger: 'blur' }
          ],
          price: [
            { required: true, message: '购买价钱不能为空', trigger: 'blur' }
          ],
          totalPrice: [
            { required: true, message: '商品总价格不能为空', trigger: 'blur' }
          ],
          code: [
            { required: true, message: '支付code不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ],
          paytime: [
            { required: true, message: '支付时间不能为空', trigger: 'blur' }
          ],
          payType: [
            { required: true, message: '支付方式1:微信2:支付宝不能为空', trigger: 'blur' }
          ],
          account: [
            { required: true, message: '充值账号不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '是否已经充值0:未充值 1:已经充值不能为空', trigger: 'blur' }
          ],
          topUpWay: [
            { required: true, message: '该商品充值方式1:卡密2:自动充值3:手动充值不能为空', trigger: 'blur' }
          ],
          password: [
            { required: true, message: '卡密不能为空', trigger: 'blur' }
          ],
          url: [
            { required: true, message: '充值官方网站不能为空', trigger: 'blur' }
          ],
          isPay: [
            { required: true, message: '是否支付0未支付1已支付不能为空', trigger: 'blur' }
          ],
          userId: [
            { required: true, message: '用户id不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.orderid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.orderid) {
            this.$http({
              url: this.$http.adornUrl(`/generator/bananaorder/info/${this.dataForm.orderid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.goodsid = data.bananaOrder.goodsid
                this.dataForm.title = data.bananaOrder.title
                this.dataForm.pic = data.bananaOrder.pic
                this.dataForm.count = data.bananaOrder.count
                this.dataForm.price = data.bananaOrder.price
                this.dataForm.totalPrice = data.bananaOrder.totalPrice
                this.dataForm.code = data.bananaOrder.code
                this.dataForm.createTime = data.bananaOrder.createTime
                this.dataForm.paytime = data.bananaOrder.paytime
                if (data.bananaOrder.payType == 1) {
                  this.dataForm.payType = '微信'
                }else if (data.bananaOrder.payType == 2) {
                  this.dataForm.payType = '支付宝'
                }              
                this.dataForm.account = data.bananaOrder.account
                if (data.bananaOrder.status == 0) {
                  this.dataForm.status = '未充值'
                }else if (data.bananaOrder.status == 1) {
                  this.dataForm.status = '已经充值'
                }
                if (data.bananaOrder.topUpWay == 1) {
                  this.dataForm.topUpWay = '卡密'
                }else if (data.bananaOrder.topUpWay == 2) {
                  this.dataForm.topUpWay = '自动充值'
                }else if (data.bananaOrder.topUpWay == 3) {
                  this.dataForm.topUpWay = '手动充值'
                }
                this.dataForm.password = data.bananaOrder.password
                this.dataForm.url = data.bananaOrder.url
                if (data.bananaOrder.isPay == 0) {
                  this.dataForm.isPay = '未支付'
                }else if (data.bananaOrder.isPay == 1) {
                  this.dataForm.isPay = '已支付'
                }
                this.dataForm.userId = data.bananaOrder.bananaUserEntity.phone
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
              url: this.$http.adornUrl(`/generator/bananaorder/${!this.dataForm.orderid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'orderid': this.dataForm.orderid || undefined,
                'goodsid': this.dataForm.goodsid,
                'title': this.dataForm.title,
                'pic': this.dataForm.pic,
                'count': this.dataForm.count,
                'price': this.dataForm.price,
                'totalPrice': this.dataForm.totalPrice,
                'code': this.dataForm.code,
                'createTime': this.dataForm.createTime,
                'paytime': this.dataForm.paytime,
                'payType': this.dataForm.payType,
                'account': this.dataForm.account,
                'status': this.dataForm.status,
                'topUpWay': this.dataForm.topUpWay,
                'password': this.dataForm.password,
                'url': this.dataForm.url,
                'isPay': this.dataForm.isPay,
                'userId': this.dataForm.userId
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
