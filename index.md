# タイトル
BootstrapVue適用時のコンポーネント間の双方向データバインディングについて


# 実行環境
Vue.js : 2.6.14
VueCLI : 4.5.15
BootstrapVue : 2.1.0


# 期待する動作
BootstrapVueで作成したラジオボタンのどちらかを選択した場合に、次のラジオボタンが出現するようにしたい


# エラー内容
```
"TypeError: Cannot read properties of undefined (reading 'value')" vue.runtime.esm.js?2b0e:619 [Vue warn]: Error in v-on handler: 
found in
---> <BFormRadioGroup>
       <RadioBtn> at src/components/RadioBtn.vue
         <QuestionContainer> at src/components/QuestionContainer.vue
           <Step2> at src/views/Step2.vue
             <App> at src/App.vue
               <Root>

TypeError: Cannot read properties of undefined (reading 'value') vue.runtime.esm.js?2b0e:1897 
    at VueComponent.updateCheckFlg (RadioBtn.vue?eb23:19)
    at invokeWithErrorHandling (vue.runtime.esm.js?2b0e:1863)
    at VueComponent.invoker (vue.runtime.esm.js?2b0e:2188)
    at invokeWithErrorHandling (vue.runtime.esm.js?2b0e:1863)
    at VueComponent.Vue.$emit (vue.runtime.esm.js?2b0e:3903)
    at VueComponent.localChecked (form-radio-check-group.js?f32e:91)
    at invokeWithErrorHandling (vue.runtime.esm.js?2b0e:1863)
    at Watcher.run (vue.runtime.esm.js?2b0e:4584)
    at flushSchedulerQueue (vue.runtime.esm.js?2b0e:4326)
    at Array.eval (vue.runtime.esm.js?2b0e:1989)
```


# 発生している問題
BootstrapVueで作成したラジオボタンのどちらかを選択した場合に、次のラジオボタンが出現するようにしたいのですが、上記のようなエラーが発生してしまいます。
BootstrapVue適用前に作成したラジオボタンではうまく動作したため、同じ記述(propsと$emit関係)をしましたが、うまく動作しません。


# 試した/調べたこと
## 試したこと
* /views/Step2.vueに記述した「v-model」を、「v-bind:カスタム属性」と「v-on:カスタムイベント」に分けて実行
 ⇨同じエラーが発生

## 調べたこと
* 公式ドキュメント
 BootstrapVueで作成したラジオボタンをバインディングする際に特殊な記述が必要ではないかどうか

* コンポーネント間の双方向データバインディングについて


# 該当のソースコード
## /components/RadioBtn.vue
### html
```html
<template>
    <b-form-radio-group
      class="mx-1"
      :options="options"
      value-field="value"
      text-field="label"
      @input="updateCheckFlg"
    ></b-form-radio-group>
  </template>
   ```
  
  ### JavaScript
  ```JavaScript
  <script>
  export default {
    props: {
      options: { type: Array, required: true },
    },
    methods: {
      updateCheckFlg: function (event) {
        this.$emit("input", event.target.value);
      },
    },
  };
  </script>
```
 

## /views/Step2.vue
### html
```html
<template>
    <div>
      <QuestionContainer
        id_number="step2"
        question_detail="以下にお答えください"
        step_number="STEP2"
      >
        <section>
          <div>
            <p class="text-primary">現在、生命保険に加入されていますか？</p>
            <RadioBtn v-model="q1CheckFlg" :options="options"></RadioBtn>
          </div>
  
          <div class="mt-5" v-show="q1CheckFlg">
            <p class="text-primary">
              現在、入院中ですか？
              または,最近3ヶ月以内に医師の診察・検査の結果、入院・手術を勧められたことはありますか？
            </p>
            <RadioBtn v-model="q2CheckFlg" :options="options"></RadioBtn>
          </div>
  
          <div class="mt-5" v-show="q2CheckFlg">
            <p class="text-primary">
              過去5年以内に病気や怪我で手術を受けたこと、または継続して7日以上の入院をしたことがありますか？
            </p>
            <RadioBtn :options="options"></RadioBtn>
          </div>
        </section>
      </QuestionContainer>
  
      <div class="text-center">
        <BackToPrevBtn :step_number="prevStepNumber" />
        <GoNextBtn :step_number="nextStepNumber" />
      </div>
    </div>
  </template>
  ```
  
  ### JavaScript
  ```JavaScript
  <script>
  import BackToPrevBtn from "../components/BackToPrevBtn.vue";
  import GoNextBtn from "../components/GoNextBtn.vue";
  import QuestionContainer from "../components/QuestionContainer.vue";
  import RadioBtn from "../components/RadioBtn.vue";
  export default {
    name: "step2",
    components: { BackToPrevBtn, GoNextBtn, QuestionContainer, RadioBtn },
    data() {
      return {
        q1CheckFlg: false,
        q2CheckFlg: false,
        prevStepNumber: "",
        nextStepNumber: "STEP3",
        options: [
          {
            label: "はい",
            value: "1",
          },
          {
            label: "いいえ",
            value: "2",
          },
        ],
      };
    },
  };
  </script>
```