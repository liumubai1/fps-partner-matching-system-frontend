<template>
  <template v-if="user">
    <van-cell title="昵称" is-link to="/user/edit" :value="user.username"  @click="toEdit('username', '昵称', user.username)"/>
    <van-cell title="账号" :value="user.userAccount"/>
    <van-cell title="头像" is-link to="/user/edit" :value="user.avatarUrl" @click="toEdit('avatarUrl', '头像URL', user.avatarUrl)"/>
    <van-cell title="电话" is-link to="/user/edit" :value="user.phone" @click="toEdit('phone', '电话', user.phone)"/>
    <van-cell title="邮箱" is-link to="/user/edit" :value="user.email" @click="toEdit('email', '邮箱', user.email)"/>
    <van-cell title="注册时间" :value="user.createTime"/>
<!--    <van-cell title="标签列表" is-link to="/user/edit" :value="user.tags" @click="toEdit('tags', '标签列表', user.tags)"/>-->
    <van-cell title="标签列表" is-link to="/user/edit" :value="user.tags" @click="toEdit('tags', '标签列表', user.tags)" />

  </template>
</template>

<script setup lang="ts">
import {useRouter} from "vue-router";
import {onMounted, ref} from "vue";
import myAxios from "../plugins/myAxios";
import {Toast} from "vant";
import {getCurrentUser} from "../services/user";


const user = ref();

onMounted(async () => {
  user.value = await getCurrentUser();
})

const router = useRouter();

const toEdit = (editKey: string, editName: string, currentValue: string) => {
  router.push({
    path: '/user/edit',
    query: {
      editKey,
      editName,
      currentValue,
      currentValue: editKey === 'tags' ? '' : currentValue,
    }
  })
}
</script>

<style scoped>

</style>