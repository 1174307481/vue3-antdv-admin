<template>
  <div class="register-box">
    <div class="register-logo">
      <img src="~@/assets/images/logo.png" width="45" />
      <h1 class="mb-0 ml-2 text-3xl font-bold">Antdv Admin 注册</h1>
    </div>
    <a-form layout="horizontal" :model="registerFormModel" @submit.prevent="handleSubmit">
      <a-form-item>
        <a-input v-model:value="registerFormModel.username" size="large" placeholder="用户名">
          <template #prefix> <Icon icon="ant-design:user-outlined" /> </template>
        </a-input>
      </a-form-item>
      <a-form-item>
        <a-input
          v-model:value="registerFormModel.password"
          size="large"
          type="password"
          placeholder="密码"
          autocomplete="new-password"
        >
          <template #prefix> <Icon icon="ant-design:lock-outlined" /></template>
        </a-input>
      </a-form-item>
      <a-form-item>
        <a-select v-model:value="registerFormModel.lang" size="large" placeholder="选择语言">
          <a-select-option value="en">English</a-select-option>
          <a-select-option value="zh">中文</a-select-option>
        </a-select>
      </a-form-item>
      <a-form-item>
        <a-button type="primary" html-type="submit" size="large" :loading="loading" block>
          注册
        </a-button>
      </a-form-item>
    </a-form>
  </div>
</template>

<script setup lang="ts">
  import { ref } from 'vue';
  import { useRouter } from 'vue-router';
  import { message, Modal } from 'ant-design-vue';
  import { Icon } from '@/components/basic/icon';
  import Api from '@/api/';
  import { to } from '@/utils/awaitTo';

  const router = useRouter();

  const loading = ref(false);
  const registerFormModel = ref({
    username: '',
    password: '',
    lang: '',
  });

  const handleSubmit = async () => {
    const { username, password, lang } = registerFormModel.value;
    if (username.trim() === '' || password.trim() === '') {
      return message.warning('用户名或密码不能为空！');
    }
    if (!lang) {
      return message.warning('请选择语言！');
    }
    message.loading('注册中...', 0);
    loading.value = true;
    console.log(registerFormModel.value);

    const [err] = await to(Api.auth.authRegister(registerFormModel.value));
    if (err) {
      Modal.error({
        title: () => '提示',
        content: () => err.message,
      });
    } else {
      message.success('注册成功！');
      setTimeout(() => router.push('/login'), 1000);
    }
    loading.value = false;
    message.destroy();
  };
</script>

<style lang="less" scoped>
  .register-box {
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 100vw;
    height: 100vh;
    padding-top: 240px;
    background: url('@/assets/login.svg');
    background-size: 100%;

    .register-logo {
      display: flex;
      align-items: center;
      margin-bottom: 30px;

      .svg-icon {
        font-size: 48px;
      }
    }

    :deep(.ant-form) {
      width: 400px;

      .ant-col {
        width: 100%;
      }

      .ant-form-item-label {
        padding-right: 6px;
      }
    }
  }
</style>
