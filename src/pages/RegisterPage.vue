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

      <!-- 性别选择 -->
      <van-field name="gender" label="性别">
        <template #input>
          <van-radio-group v-model="gender" direction="horizontal">
            <van-radio name="male">男</van-radio>
            <van-radio name="female">女</van-radio>
          </van-radio-group>
        </template>
      </van-field>

      <!-- 标签选择组件 -->
      <van-divider content-position="left">游戏标签（多选）</van-divider>
      <div v-if="selectedTags.length === 0" style="padding: 0 16px; color: #969799;">
        请选择至少一个游戏标签
      </div>
      <van-row gutter="16" style="padding: 0 16px">
        <van-col v-for="tag in selectedTags" :key="tag" style="margin-bottom: 8px;">
          <van-tag closeable size="small" type="primary" @close="removeTag(tag)">
            {{ tag }} <!-- 直接显示中文 -->
          </van-tag>
        </van-col>
      </van-row>
      <van-tree-select
          v-model:active-id="selectedTags"
          :items="tagOptions"
          :main-active-index="0"
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
import { ref } from "vue";
import myAxios from "../plugins/myAxios";
import { Toast } from "vant";
import { useRouter } from "vue-router";

const router = useRouter();

// 表单数据
const userAccount = ref("");
const userPassword = ref("");
const checkPassword = ref("");
const gender = ref("male");
const selectedTags = ref<string[]>([]); // 存储选中标签的中文

// 游戏标签选项配置（使用中文作为id）
const tagOptions = [
  {
    text: "FPS游戏",
    children: [
      { text: "apex", id: "apex" },
      { text: "csgo", id: "csgo" },
      { text: "守望先锋", id: "守望先锋" },
      { text: "Valorant", id: "Valorant" },
      { text: "三角洲行动", id: "三角洲行动" },
    ],
  },
];

// 移除标签
const removeTag = (tag: string) => {
  selectedTags.value = selectedTags.value.filter((t) => t !== tag);
};

// 提交表单
const onSubmit = async () => {
  // 密码验证
  if (userPassword.value !== checkPassword.value) {
    Toast.fail("两次输入的密码不一致");
    return;
  }

  // 标签验证
  if (selectedTags.value.length === 0) {
    Toast.fail("请至少选择一个游戏标签");
    return;
  }

  try {
    const res = await myAxios.post("/user/register", {
      userAccount: userAccount.value,
      userPassword: userPassword.value,
      checkPassword: checkPassword.value,
      gender: gender.value,
      tags: selectedTags.value, // 直接传递中文标签
    });

    if (res.code === 0 && res.data) {
      Toast.success("注册成功");
      router.push("/user/login");
    } else {
      Toast.fail(res.description || "注册失败");
    }
  } catch (error) {
    Toast.fail("注册请求失败");
  }
};
</script>

<style scoped>
/* 自定义样式 */
.van-tag {
  margin-right: 8px;
}
</style>