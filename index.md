# タイトル
firebaseを使用したユーザー登録について


# 実行環境
下記コードはconfig.jsの内容です。
```
{
  "name": "vue-socialtipping-app",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint"
  },
  "dependencies": {
    "core-js": "^3.6.5",
    "firebase": "^9.6.6",
    "vue": "^2.6.11",
    "vue-router": "^3.2.0",
    "vuelidate": "^0.7.7",
    "vuex": "^3.4.0"
  },
  "devDependencies": {
    "@fullhuman/postcss-purgecss": "^4.1.3",
    "@vue/cli-plugin-babel": "~4.5.0",
    "@vue/cli-plugin-eslint": "~4.5.0",
    "@vue/cli-plugin-router": "~4.5.0",
    "@vue/cli-plugin-vuex": "~4.5.0",
    "@vue/cli-service": "~4.5.0",
    "autoprefixer": "^9.8.8",
    "babel-eslint": "^10.1.0",
    "eslint": "^6.7.2",
    "eslint-plugin-vue": "^6.2.2",
    "postcss": "^7.0.39",
    "tailwindcss": "npm:@tailwindcss/postcss7-compat@^2.2.17",
    "vue-template-compiler": "^2.6.11"
  }
}
```


# 期待する動作
登録ボタンを押したらユーザーが登録される


# エラー内容
特になし


# 発生している問題
「新規登録」ボタンを押してもユーザー登録に成功せず、失敗した場合の処理に移ってしまいます。
ユーザー登録処理のコードは、公式ドキュメントの物(ウェブバージョン9)を使用しています。


# 試した/調べたこと
## 試したこと
* ユーザー登録処理のコードを、ウェブバージョン8に変更
  ⇨改善されず

## 調べたこと
* 公式ドキュメント
* firebaseのユーザー登録周りの処理について


# 該当のソースコード
## src/firebase/firebase.js
```
import { initializeApp } from "firebase/app";
import { getAuth, createUserWithEmailAndPassword } from "firebase/auth";

const firebaseConfig = {
  //firebaseでプロジェクトを作成した際に表示されたconfigをコピーして貼り付け//
};
const app = initializeApp(firebaseConfig);

const auth = getAuth(app);

export const registerUser = (email, password) => {
  createUserWithEmailAndPassword(auth, email, password)
    .then((userCredential) => {
      // Signed in
      const user = userCredential.user;
      alert(user);
      // ...
    })
    .catch(() => {
      alert("登録失敗");
      // ..
    });
};
```

##src/view/UserRegistration.vue
```
<template>
  <div class="container mx-auto">
    <p class="mb-20 text-center text-4xl">新規登録画面</p>
    <div class="mt-8">
      <form class="w-10/12 mx-auto md:max-w-md">
        <FormInput
          v-model="userName"
          label="ユーザー名"
          type="text"
          placeHolder="ユーザー名を入力してください"
        />
        <FormInput
          v-model="email"
          label="メールアドレス"
          type="email"
          placeHolder="メールアドレスを入力してください"
        />
        <FormInput
          v-model="password"
          label="パスワード"
          type="password"
          placeHolder="パスワードを入力してください"
        />
        <button
          @click="register"
          class="
            text-blue-500
            block
            px-2
            py-1
            border border-blue-500
            font-semibold
            rounded
            hover:bg-blue-100
            mx-auto
          "
        >
          新規登録
        </button>
      </form>
    </div>
  </div>
</template>

<script>
import FormInput from "../components/FormInput.vue";
import { registerUser } from "../firebase/firebase";
export default {
  name: "UserRegistration",
  components: { FormInput },
  data() {
    return {
      userName: "",
      email: "",
      password: "",
    };
  },
  methods: {
    register() {
      registerUser(this.email, this.password);
    },
  },
};
</script>
```