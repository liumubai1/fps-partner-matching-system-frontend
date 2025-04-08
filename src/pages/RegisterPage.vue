<template>
  <van-form @submit="onSubmit">
    <van-cell-group inset>
      <van-field
          v-model="userAccount"
          name="userAccount"
          label="账号"
          placeholder="请输入账号"
          :rules="[{ required: true, message: '请填写用户名' }]"
      />
      <van-field
          v-model="userPassword"
          type="password"
          name="userPassword"
          label="密码"
          placeholder="请输入密码"
          :rules="[{ required: true, message: '请填写密码' }]"
      />
      <van-field
          v-model="checkPassword"
          type="password"
          name="checkPassword"
          label="确认密码"
          placeholder="请再次输入密码"
          :rules="[{ required: true, message: '请再次输入密码' }]"
      />
      <van-field
          v-model="planetCode"
          name="planetCode"
          label="星球编号"
          placeholder="请输入星球编号"
          :rules="[{ required: true, message: '请输入星球编号' }]"
      />
    </van-cell-group>
    <div style="margin: 16px;">
      <van-button round block type="primary" native-type="submit">
        注册
      </van-button>
      <div style="margin-top: 16px; text-align: center;">
        <span style="color: #969799; font-size: 14px;">已有账号？</span>
        <router-link to="/user/login" style="color: #1989fa; font-size: 14px;">去登录</router-link>
      </div>
    </div>
  </van-form>
</template>

<script setup lang="ts">
import {ref} from "vue";
import myAxios from "../plugins/myAxios";
import {Toast} from "vant";
import {useRouter} from "vue-router";

const router = useRouter();

const userAccount = ref('');
const userPassword = ref('');
const checkPassword = ref('');
const planetCode = ref('');

const onSubmit = async () => {
  if (userPassword.value !== checkPassword.value) {
    Toast.fail('两次输入的密码不一致');
    return;
  }

  const res = await myAxios.post('/user/register', {
    userAccount: userAccount.value,
    userPassword: userPassword.value,
    checkPassword: checkPassword.value,
    planetCode: planetCode.value,
  });

  console.log(res, '用户注册');
  if (res.code === 0 && res.data) {
    Toast.success('注册成功');
    // 注册成功后跳转到登录页面
    router.push('/user/login');
  } else {
    Toast.fail('注册失败');
  }
};
</script>

<style scoped>

</style>