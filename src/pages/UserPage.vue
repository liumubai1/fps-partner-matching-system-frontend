<template>
  <template v-if="user">
    <van-cell title="当前用户" :value="user?.username" />
    <van-cell title="修改信息" is-link to="/user/update" />
    <van-cell title="我创建的队伍" is-link to="/user/team/create" />
    <van-cell title="我加入的队伍" is-link to="/user/team/join" />
    <div style="text-align: right; margin: 12px;">
      <van-button size="small" type="danger" @click="logout">
        退出登录
      </van-button>
    </div>
  </template>
</template>

<script setup lang="ts">
import { useRouter } from "vue-router";
import { onMounted, ref } from "vue";
import myAxios from "../plugins/myAxios";
import { Toast } from "vant";
import { getCurrentUser } from "../services/user";

const user = ref();
const router = useRouter();

onMounted(async () => {
  user.value = await getCurrentUser();
})

const toEdit = (editKey: string, editName: string, currentValue: string) => {
  router.push({
    path: '/user/edit',
    query: {
      editKey,
      editName,
      currentValue,
    }
  })
}

const logout = async () => {
  try {
    const res = await myAxios.post('/user/logout');
    if (res.code === 0) {
      Toast.success("已退出登录");
      user.value = null;
      router.push("/user/login"); // 退出后跳转首页
    } else {
      Toast.fail("退出失败：" + res.message);
    }
  } catch (e) {
    Toast.fail("网络错误，退出失败");
  }
}
</script>


<style scoped>

</style>
