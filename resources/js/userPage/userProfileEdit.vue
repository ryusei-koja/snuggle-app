<template>
  <v-container class="profile-edit" v-if="userData">
    <v-text-field v-model="userData.name" type="text" label="お名前" outlined></v-text-field>

    <v-textarea outlined label="自己紹介文" auto-grow v-model="userData.introduction"></v-textarea>
    <v-select
      :items="prefectures"
      item-text="name"
      item-value="name"
      label="地域選択"
      v-model="userData.region"
      outlined
    ></v-select>
    <v-file-input
      accept="image/png, image/jpeg"
      multiple
      label="プロフィール画像"
      prepend-icon="mdi-camera"
      show-size
      v-on:change="confirmImage"
    ></v-file-input>

    <p>
      <v-img
        v-if="!confirmedImageShow && userData.image.match('userImages')"
        :src="`https://snuggle-app.s3.ap-northeast-1.amazonaws.com/${beforeUserImage}`"
        height="200"
        width="250"
      />
      <v-img
        v-else-if="!confirmedImageShow"
        :src="`/storage/userImages/${beforeUserImage}`"
        height="200"
        width="250"
      />
      <v-img v-else-if="confirmedImageShow" :src="confirmedImage" height="200" width="250" />
    </p>
    <div class="btn-wrap">
      <v-btn class="save-btn" @click="update" color="#F6BBA6" width="250" height="50">
        保存
      </v-btn>
    </div>
  </v-container>
</template>

<script>
import prefectureData from '../prefectureData.json';
export default {
  data: () => ({
    userData: '',
    prefectures: [],
    beforeUserImage: '',
    confirmedImage: '',
    confirmedImageShow: '',
  }),
  computed: {
    user() {
      return this.$store.getters['auth/user'];
    },
  },
  mounted() {
    this.prefectures = prefectureData;
    const token = this.$store.getters['auth/token'];

    //トークンがあるかチェックなければログイン画面へ
    if (!token) {
      this.$router.push({ name: 'userLogin' });
    }

    const user = this.$store.getters['auth/user'];
    if (token && !this.userData && token != 'null') {
      this.$store.dispatch('auth/fetchUser');
    }
  },
  watch: {
    user: function(userData, undefind) {
      this.userData = userData;
      this.beforeUserImage = userData.image;
    },
  },
  methods: {
    update() {
      let updateFormData = new FormData();
      updateFormData.append('id', this.userData.id);
      updateFormData.append('name', this.userData.name);
      updateFormData.append('region', this.userData.region);
      updateFormData.append('introduction', this.userData.introduction);
      updateFormData.append('image', this.userData.image);
      this.$store.dispatch('auth/userUpdate', updateFormData);
    },

    //確認用画像
    confirmImage(e) {
      if (e[0]) {
        this.userData.image = e[0];
        this.createImage(this.userData.image);
        this.confirmedImageShow = true;
      } else {
        this.confirmedImageShow = false;
        this.userData.image = 'null';
      }
    },

    createImage(image) {
      let reader = new FileReader();
      reader.readAsDataURL(image);
      reader.onload = e => {
        this.confirmedImage = e.target.result;
      };
    },
  },
};
</script>

<style scoped>
.profile-edit {
  margin-top: 100px;
  margin-bottom: 100px;
}

.btn-wrap {
  width: 100%;
  display: flex;
  justify-content: center;
}
</style>
