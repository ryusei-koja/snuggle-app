<template>
  <div>
    <v-container>
      <div class="login-form">
        <v-form @submit.prevent="register" ref="form">
          <h1 class="text-center mb-5">ログイン</h1>
          <v-text-field
            v-model="loginForm.email"
            type="email"
            label="メールアドレス"
            color="#73a776"
            :rules="emailRules"
            placeholder="example@example.com"
            outlined
          ></v-text-field>
          <v-text-field
            v-model="loginForm.password"
            type="password"
            color="#73a776"
            label="パスワード"
            :rules="passwordRules"
            outlined
          ></v-text-field>
        </v-form>
        <div class="mb-5 d-flex justify-space-between">
          <router-link :to="{ name: 'userRegister' }">
            <v-btn width="150" outlined text color="#73a776">
              新規登録はこちら
            </v-btn>
          </router-link>
          <v-btn @click="login" width="150" outlined text color="#73a776">ログイン</v-btn>
        </div>
        <div class="d-flex justify-center">
          <v-btn @click="gestLogin" outlined text color="#73a776">ゲストユーザーでログイン</v-btn>
        </div>
      </div>
    </v-container>
  </div>
</template>

<script>
export default {
  data: () => ({
    loginForm: {
      email: '',
      password: '',
    },
    emailRules: [
      v => !!v || 'メールアドレスを入力してください。',
      v => /.+@.+\..+/.test(v) || 'メールアドレスは不正な値です。',
    ],
    passwordRules: [
      v => !!v || 'パスワードを入力してください。',
      v => v.length >= 8 || ' 8文字以上です。 ',
    ],
  }),
  methods: {
    login() {
      if (this.$refs.form.validate()) {
        this.$store.dispatch('auth/login', this.loginForm);
      }
    },
    gestLogin() {
      this.$store.dispatch('auth/login', {
        email: 'test@test.com',
        password: 'test1111',
      });
    },
  },
};
</script>

<style scoped>
.login-form {
  max-width: 500px;
  margin: 180px auto 100px;
  border: 1px solid #73a776;
  border-radius: 10px;
  padding: 30px;
}
</style>
