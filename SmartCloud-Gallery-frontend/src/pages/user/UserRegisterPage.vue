<template>
  <div id="userRegisterPage">
    <h2 class="title">影智云图库 - 用户注册</h2>
    <a-form :model="formState" name="basic" autocomplete="off" @finish="handleSubmit">
      <a-form-item name="userAccount" :rules="[{ required: true, message: '请输入用户名!' }]">
        <a-input v-model:value="formState.userAccount" placeholder="请输入账号" />
      </a-form-item>

      <a-form-item
        name="userPassword"
        :rules="[
          { required: true, message: '请输入密码!' },
          { min: 6, message: '密码长度不能小于6位' },
        ]"
      >
        <a-input-password v-model:value="formState.userPassword" placeholder="请输入密码" />
      </a-form-item>

      <a-form-item
        name="checkPassword"
        :rules="[
          { required: true, message: '请输入确认密码!' },
          { min: 6, message: '确认密码长度不能小于6位' },
        ]"
      >
        <a-input-password v-model:value="formState.checkPassword" placeholder="请输入确认密码" />
      </a-form-item>

      <div class="tip">
        有账号?
        <RouterLink to="/user/login">去登录</RouterLink>
      </div>

      <a-form-item>
        <a-button type="primary" html-type="submit" style="width: 100%">注册</a-button>
      </a-form-item>
    </a-form>
  </div>
</template>
<script lang="ts" setup>
import { reactive } from 'vue'
import { userLoginUsingPost, userRegisterUsingPost } from '@/api/userController.ts'
import { useLoginUserStore } from '@/stores/useLoginUserStore.ts'
import { message } from 'ant-design-vue'
import { useRouter } from 'vue-router'

const formState = reactive<API.UserRegisterRequest>({
  userAccount: '',
  userPassword: '',
  checkPassword: '',
})

const router = useRouter()
const loginUserStore = useLoginUserStore()
const handleSubmit = async (values: any) => {
  if (formState.userPassword !== formState.checkPassword) {
    message.error('两次输入的密码不一致')
    return
  }

  try {
    const res = await userRegisterUsingPost(values)
    if (res.data.code === 0 && res.data.data) {
      message.success('注册成功')
      await router.push({
        path: '/user/login',
        replace: true,
      })
    } else {
      message.error('注册失败' + res.data.message)
    }
  } catch (e: any) {
    message.error('注册失败' + e.message)
  }
}
</script>

<style scoped lang="scss">
#userRegisterPage {
  padding-top: 20px;
  max-width: 360px;
  margin: 0 auto;
  .title {
    text-align: center;
    margin-bottom: 16px;
  }
  .tip {
    margin-bottom: 16px;
    color: #bbb;
    font-size: 13px;
    text-align: right;
  }
}
</style>
