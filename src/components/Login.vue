<template>
  <el-row class="content">
    <el-col :xs="24" :sm="{span: 6,offset: 9}">
      <span class="title">
       欢迎登录
      </span>
      <el-row>
        <el-input
          v-model="account"
          placeholder="账号"
          type="text">
        </el-input>
        <el-input
          v-model="password"
          placeholder="密码"
          type="password">
          @keyup.enter.native="loginToDo">
        </el-input>
        <el-button type="primary" @click="loginToDo">登录</el-button>
      </el-row>
    </el-col>
  </el-row>
</template>
<script>
export default {
  data () {
    return {
      account: '',
      password: ''
    }
  },
  mounted: function () {
    this.$http.get('/login')
      .then((res) => {
        console.log(res.data)
        if (res.data) {
          this.csrf_token = res.data.match(/name="csrf_token" type="hidden" value="(.*?)">/)[1]
        }
        console.log('csrf_token: ' + this.csrf_token)
      })
  },
  methods: {
    loginToDo  () {
      let obj = {
        email: this.account,
        password: this.password,
        csrf_token: this.csrf_token
      }
      console.log(obj)
      this.$http.post('/login', obj) // 将信息发送给后端
        .then((res) => {
          console.log(res)
          if (res.data) {
            // console.log(res.data.response.user.authentication_token)
            sessionStorage.setItem('token', res.data.response.user.authentication_token) // 用sessionStorage把token存下来
            this.$message({ // 登录成功，显示提示语
              type: 'success',
              message: '登录成功！'
            })
            // this.$router.push('/todolist') // 进入todolist页面，登录成功
          } else {
            this.$message.error(res.data.info) // 登录失败，显示提示语
            sessionStorage.setItem('token', null) // 将token清空
          }
        }, (err) => {
          if (err) {
            this.$message.error('请求错误！')
            sessionStorage.setItem('token', null) // 将token清空
          }
        }
      )
    }
  }
}
</script>
<style lang="stylus" scoped>
  .el-row.content
    padding 16px
  .title
    font-size 28px
  .el-input
    margin 12px 0
  .el-button
    width 100%
    margin-top 12px
</style>
