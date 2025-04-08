<template>
  <user-card-list :user-list="userList" :loading="loading" />
  <van-empty v-if="!loading && (!userList || userList.length < 1)" description="搜索结果为空" />
</template>

<script setup lang="ts">
import {onMounted, ref} from 'vue';
import {useRoute} from "vue-router";
import myAxios from "../plugins/myAxios";
import {Toast} from "vant";
import qs from 'qs';
import UserCardList from "../components/UserCardList.vue";

const route = useRoute();
const {tags} = route.query;

const userList = ref<UserType[]>([]);
const loading = ref(true); // 添加loading状态

onMounted(async () => {
  loading.value = true; // 开始加载
  try {
    const userListData = await myAxios.get('/user/search/tags', {
      params: {
        tagNameList: tags
      },
      paramsSerializer: params => {
        return qs.stringify(params, {indices: false})
      }
    })
        .then(function (response) {
          console.log('/user/search/tags succeed', response);
          return response?.data;
        })
        .catch(function (error) {
          console.error('/user/search/tags error', error);
          Toast.fail('请求失败');
          return [];
        });

    if (userListData) {
      userListData.forEach(user => {
        if (user.tags) {
          user.tags = JSON.parse(user.tags);
        }
      });
      userList.value = userListData;
    }
  } finally {
    loading.value = false; // 结束加载
  }
});
</script>

<style scoped>
</style>