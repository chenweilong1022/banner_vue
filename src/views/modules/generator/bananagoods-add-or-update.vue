<template>

  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="商品名称" prop="title">
      <el-input v-model="dataForm.title" placeholder="商品名称"></el-input>
    </el-form-item>
    <el-form-item label="商品列表图片" prop="indexPic">
      <el-upload
        class="avatar-uploader"
        :action="url"
        :show-file-list="false"
        naem="file"
        :on-success="handleAvatarSuccessIndexPic">
        <img v-if="dataForm.indexPic" :src="dataForm.indexPic" class="avatar">
        <i v-else class="el-icon-plus avatar-uploader-icon"></i>
      </el-upload>
    </el-form-item>

    <el-form-item label="商品详情图片" prop="pic">
      <el-upload
        class="avatar-uploader"
        :action="url"
        :show-file-list="false"
        naem="file"
        :on-success="handleAvatarSuccess">
        <img v-if="dataForm.pic" :src="dataForm.pic" class="avatar">
        <i v-else class="el-icon-plus avatar-uploader-icon"></i>
      </el-upload>
    </el-form-item>

    <!-- <el-form-item label="步骤富文本" prop="steps">
      <el-input v-model="dataForm.steps" placeholder="步骤富文本"></el-input>
    </el-form-item> -->
    <el-form-item label="价格" prop="price">
      <el-input v-model="dataForm.price" placeholder="价格"></el-input>
    </el-form-item>
    <el-form-item label="分享价格" prop="shaPrice">
      <el-input v-model="dataForm.shaPrice" placeholder="分享价格"></el-input>
    </el-form-item>
    <el-form-item label="充值方式" prop="topUpWay">
      <el-select v-model="dataForm.topUpWay" placeholder="请选择">
        <el-option
          v-for="item in options"
          :key="item.value"
          :label="item.label"
          :value="item.value">
        </el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="充值官网" prop="exchangeAddress">
      <el-input v-model="dataForm.exchangeAddress" placeholder="卡密兑换地址--充值官网"></el-input>
    </el-form-item>
    <el-form-item label="分享url" prop="shareUrl">
      <el-input v-model="dataForm.shareUrl" placeholder="分享url"></el-input>
    </el-form-item>
    <el-form-item label="分享标题" prop="shareTitle">
      <el-input v-model="dataForm.shareTitle" placeholder="分享标题"></el-input>
    </el-form-item>
    <el-form-item label="分享描述" prop="shareDesc">
      <el-input v-model="dataForm.shareDesc" placeholder="分享描述"></el-input>
    </el-form-item>
    <el-form-item label="提示内容" prop="prompt">
      <el-input v-model="dataForm.prompt" placeholder="提示内容"></el-input>
    </el-form-item>
    <el-form-item label="状态" prop="status">
        <el-radio v-model="dataForm.status" label="0">正常</el-radio>
        <el-radio v-model="dataForm.status" label="-1">下架</el-radio>
    </el-form-item>

    <el-form-item label="状态" prop="status">


  <div class="mod-demo-ueditor">
    <el-alert
      title="提示："
      type="warning"
      :closable="false">
      <div slot-scope="description">
        <p class="el-alert__description">1. 此Demo只提供UEditor官方使用文档，入门部署和体验功能。具体使用请参考：http://fex.baidu.com/ueditor/</p>
        <p class="el-alert__description">2. 浏览器控制台报错“请求后台配置项http错误，上传功能将不能正常使用！”，此错需要后台提供上传接口方法（赋值给serverUrl属性）</p>
      </div>
    </el-alert>

    <script :id="ueId" class="ueditor-box" type="text/plain" style="width: 100%; height: 260px;">hello world!</script>
    
    <!-- 获取内容 -->
    <p><el-button @click="getContent()">获得内容</el-button></p>
    <el-dialog
      title="内容"
      :visible.sync="dialogVisible"
      :append-to-body="true">
      {{ ueContent }}
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
      </span>
    </el-dialog>
  </div>



    </el-form-item>





    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>

</template>

<style>

  .avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }
  .avatar-uploader .el-upload:hover {
    border-color: #409EFF;
  }
  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
  }
  .avatar {
    width: 178px;
    height: 178px;
    display: block;
  }
</style>

<script>
  import ueditor from 'ueditor'
  export default {
    name: 'UE',
    data () {
      return {
        ue: null,
        ueId: `J_ueditorBox_${new Date().getTime()}`,
        ueContent: '',
        dialogVisible: false,
        url: '',
        visible: false,
        options: [{
          value: 1,
          label: '卡密'
        }, {
          value: 2,
          label: '自动充值'
        }, {
          value: 3,
          label: '手动充值'
        }],
        dataForm: {
          goddsid: 0,
          title: '',
          pic: '',
          indexPic: '',
          price: '',
          shaPrice: '',
          topUpWay: '',
          exchangeAddress: '',
          status: '0',
          shareUrl: '',
          shareTitle: '',
          shareDesc: '',
          prompt: ''
        },
        dataRule: {
          title: [
            { required: true, message: '商品名称不能为空', trigger: 'blur' }
          ],
          pic: [
            { required: true, message: '商品详情图片不能为空', trigger: 'blur' }
          ],
          indexPic: [
            { required: true, message: '商品列表图片不能为空', trigger: 'blur' }
          ],
          price: [
            { required: true, message: '价格不能为空', trigger: 'blur' }
          ],
          shaPrice: [
            { required: true, message: '分享价格不能为空', trigger: 'blur' }
          ],
          topUpWay: [
            { required: true, message: '充值方式1:卡密2:自动充值3:手动充值不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    mounted() {
      setInterval(this.timer, 5000);
    },
    methods: {
      timer() {
        console.log(this.url)
        this.ue = ueditor.getEditor(this.ueId, {
          serverUrl: this.url, // 服务器统一请求接口路径
          zIndex: 3000
        })
      },
      getUEContent() { // 获取内容方法
        return this.editor.getContent()
      },
      init (id) {
        this.url = this.$http.adornUrl(`/file/upload?token=${this.$cookie.get('token')}`)
        this.dataForm.goddsid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.goddsid) {
            this.$http({
              url: this.$http.adornUrl(`/generator/bananagoods/info/${this.dataForm.goddsid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.title = data.bananaGoods.title
                this.dataForm.pic = data.bananaGoods.pic
                this.dataForm.indexPic = data.bananaGoods.indexPic
                this.dataForm.price = data.bananaGoods.price
                this.dataForm.shaPrice = data.bananaGoods.shaPrice
                this.dataForm.topUpWay = data.bananaGoods.topUpWay
                this.dataForm.exchangeAddress = data.bananaGoods.exchangeAddress
                this.dataForm.status = data.bananaGoods.status + ''
                this.dataForm.shareUrl = data.bananaGoods.shareUrl
                this.dataForm.shareTitle = data.bananaGoods.shareTitle
                this.dataForm.shareDesc = data.bananaGoods.shareDesc
                this.dataForm.prompt = data.bananaGoods.prompt
              }
            })
          }
        })
      },
      handleAvatarSuccess(res, file) {
        this.dataForm.pic = res.data
      },
      handleAvatarSuccessIndexPic(res, file) {
        this.dataForm.indexPic = res.data
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/generator/bananagoods/${!this.dataForm.goddsid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'goddsid': this.dataForm.goddsid || undefined,
                'title': this.dataForm.title,
                'pic': this.dataForm.pic,
                'indexPic': this.dataForm.indexPic,
                'price': this.dataForm.price,
                'shaPrice': this.dataForm.shaPrice,
                'topUpWay': this.dataForm.topUpWay,
                'exchangeAddress': this.dataForm.exchangeAddress,
                'status': this.dataForm.status,
                'shareUrl': this.dataForm.shareUrl,
                'shareTitle': this.dataForm.shareTitle,
                'shareDesc': this.dataForm.shareDesc,
                'prompt': this.dataForm.prompt
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
    },
    destroyed() {
      this.editor.destroy();
    }
  }
</script>
