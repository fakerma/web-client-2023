<head>
<!-- 其他头部内容 -->
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
</head>
<template>
  <div class="login">
    <div class="login-form">
      <h1 style="text-align: center; margin: 20px 0;">ユーザー登録</h1>
      <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="100px" class="demo-ruleForm">
        <el-form-item label="ユーザー" prop="username">
          <el-input v-model="ruleForm.username"></el-input>
        </el-form-item>
        <el-form-item label="パスワード" prop="password">
          <el-input v-model="ruleForm.password"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="submitForm('ruleForm')">ログイン</el-button>
          <el-button @click="resetForm('ruleForm')">リセツト</el-button>
        </el-form-item>
      </el-form>
      <!--      <div style="text-align: center;">-->
      <!--        <el-link href="/sys-admin/">バックグラウンド管理(一時的、ログイン不要でジャンプできます)</el-link>-->

      <!--      </div>-->
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      isDesktop: window.innerWidth > 600,
      ruleForm: {
        username: '',
        password: ''
      },
      rules: {
        username: [
          {required: true, message: 'ユーザー名を入力してください', trigger: 'blur'},
          {min: 2, max: 15, message: '文字数は2から15文字まで', trigger: 'blur'}
        ],
        password: [
          {required: true, message: 'パスワードを入力してください', trigger: 'blur'},
          {min: 4, max: 15, message: '文字数は4から15文字まで', trigger: 'blur'}
        ]
      }
    };
  },
  mounted() {
    window.addEventListener('resize', this.updateIsDesktop);
  },
  methods: {
    updateIsDesktop() {
      this.isDesktop = window.innerWidth > 600;
    },
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          var localPath = this.GLOBAL.localSrc;
          let url = '/api/users/login';
          console.log('尝试登录')
          console.log('请求路径为:' + url)
          console.log('请求参数为:' + this.ruleForm)
          console.log(this.ruleForm)
          // let dateFrom = this.qs.stringify(this.ruleForm)
          // 设置请求头为 application/json
          let config = {
            headers: {
              'Content-Type': 'application/json'
            }
          };
          this.axios.post(url, this.ruleForm, config).then((response) => {
            console.log(response);
            if (response.data.state == 20000) {
              console.log('ログインが成功しました.');
              this.$message({
                message: 'ログインが成功しました.',
                type: 'success'
              });
              const token = response.data.data.token;
              localStorage.setItem('token', token);
              this.$router.push({name: 'ChatApp'})
            } else {
              console.log('ログインに失敗しました。ユーザー名またはパスワードが正しくありません.')
              this.$notify.error({
                title: 'ログインに失敗しました.',
                message: 'ログインに失敗しました。ユーザー名またはパスワードが正しくありません.'
              });
            }
          });
        } else {
          console.log('error submit!!')
          return false;
        }
      });
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    }
  }
};
</script>
<style>
body {
  background-color: #faf0e6 !important;

}
</style>
<style scoped>


.login-form {
  width: 100%; /* 使用100%宽度，充满整个屏幕 */
  box-sizing: border-box; /* 使padding不会撑大宽度 */
  padding: 20px; /* 减小padding，以适应小屏幕 */
  background: #fff;
  margin: 0; /* 取消上下边距，以充分利用空间 */
}

/* 如果需要保持在大屏幕上的一些样式，可以使用媒体查询 */
  @media only screen and (min-width: 600px) {
    .login-form {
      width: 500px; /* 在大屏幕上保持原有宽度 */
      margin: 50px auto; /* 保持上下居中 */
    padding: 30px 50px;
  }
}

h1 {
  text-align: center;
  margin: 10px 0;
  font-size: 1.5em;
}


</style>