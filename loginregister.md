# Login app과 Register app

기본적으로 axios, json 데이터 베이스를 이용하여 만들고자 하였다.

## db.json

```json
{
  "account": [
    {
      "name": "홍길동",
      "userid": "user1",
      "password": 1234
    },
    {
      "name": "아무개",
      "userid": "user2",
      "password": 5678
    }
  ]
}
```

회원정보이다.

## Register app

```vue
<template>
  <div>
    <form @submit.prevent="addAccount">
      <fieldset>
        <legend><h2>Register</h2></legend>
        <div>
          <label>name : <input type="text" v-model="name" /></label>
        </div>
        <div>
          <label>id : <input type="text" v-model="userid" /></label>
        </div>
        <div>
          <label>pw : <input type="password" v-model="password" /></label>
        </div>
        <button type="submit">가입</button>
      </fieldset>
    </form>
  </div>
</template>
```

이름, id, 비밀번호를 입력받는 회원가입 템플릿을 간단히 구현하였다.

```javascript
import axios from "axios";

export default {
  data() {
    return {
      accounts: [],
      name: "",
      userid: "",
      password: "",
    };
  },
  methods: {
    async addAccount() {
      if (this.name === "" || this.userid === "" || this.password === "")
        return;
      const newAccount = {
        name: this.name,
        userid: this.userid,
        password: this.password,
      };
      await axios.post(`http://localhost:3000/account`, newAccount);
      alert("가입되었습니다!");
    },
  },
};
```

입력을 받은 후 가입 버튼을 누르면 실행되는 메서드이다.

## Login app

```javascript
import axios from "axios";

export default {
  data: function () {
    return {
      userid: "",
      password: "",
    };
  },
  methods: {
    submitForm: function () {
      console.log(this.userid, this.password);
      var url = "http://localhost:3000/account";
      var data = {
        userid: this.userid,
        password: this.password,
      };
      axios
        .post(url, data)
        .then(function (response) {
          console.log(response);
        })
        .catch(function (error) {
          console.log(response);
        });
    },
  },
};
```

axios를 이용해 받은 정보를 토대로 로그인 시키는 메서드이다.
axios에 대한 공부가 더 필요할 것 같다.

## 이슈

### Register

1. json 서버로 추가되지 않음
   - Uncaught (in promise) Error: Request failed with status code 500
   - Uncaught TypeError: Cannot set property './node_modules/mini-css-extract-plugin/dist/loader.js??clonedRuleSet-2.use[0]!./node_modules/css-loader/dist/cjs.js!./node_modules/vue-loader/dist/stylePostLoader.js!./node_modules/vue-loader/dist/index.js??ruleSet[1].rules[4].use[0]!./src/components/Register.vue?vue&type=style&index=0&id=7bf3755a&scoped=true&lang=css' of undefined
     at self.webpackHotUpdate_vue3_demo_home (jsonp chunk loading:81)
     at src_components_Register_vue-\_bb9d1.ed2093a8706ca93b8924.hot-update.js:1

### Login

1. axios의 post 기능이 되지 않는다.
   - POST http://localhost:3000/account 500 (Internal Server Error)
2. axios의 response 기능이 되지 않는다.
   - Login.vue:38 Uncaught (in promise) ReferenceError: response is not defined
     at Login.vue:38
