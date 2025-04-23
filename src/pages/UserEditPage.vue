<template>
  <van-form @submit="onSubmit">
    <template v-if="editUser.editKey === 'tags'">
      <van-checkbox-group v-model="editUser.currentValue" direction="vertical">
        <van-checkbox v-for="tag in tagOptions" :key="tag" :name="tag">
          {{ tag }}
        </van-checkbox>
      </van-checkbox-group>
    </template>
    <template v-else>
      <van-field
          v-model="editUser.currentValue"
          :name="editUser.editKey"
          :label="editUser.editName"
          :placeholder="`请输入${editUser.editName}`"
      />
    </template>
    <div style="margin: 16px;">
      <van-button round block type="primary" native-type="submit">
        提交
      </van-button>
    </div>
  </van-form>
</template>



<script setup lang="ts">
import {useRoute, useRouter} from "vue-router";
import { ref } from "vue";
import myAxios from "../plugins/myAxios";
import {Toast} from "vant";
import {getCurrentUser} from "../services/user";

const route = useRoute();
const router = useRouter();

const tagOptions = ['apex', 'csgo', '守望先锋', 'Valorant', '三角洲行动'];

const editUser = ref({
  editKey: route.query.editKey,
  currentValue: route.query.editKey === 'tags'
      ? (route.query.currentValue as string)?.split(',') ?? []
      : route.query.currentValue,
  editName: route.query.editName,
});






const onSubmit = async () => {
  const currentUser = await getCurrentUser();
  if (!currentUser) {
    Toast.fail('用户未登录');
    return;
  }

  const submitData: any = {
    id: currentUser.id,
  };

  submitData[editUser.value.editKey as string] =
      editUser.value.editKey === 'tags'
          ? (editUser.value.currentValue as string[]).join(',')
          : editUser.value.currentValue;

  const res = await myAxios.post('/user/update', submitData);

  if (res.code === 0 && res.data > 0) {
    Toast.success('修改成功');
    router.back();
  } else {
    Toast.fail('修改错误');
  }
};



</script>

<style scoped>

</style>
