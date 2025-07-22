<script setup>
import { EyeIcon, ShieldCheckIcon } from '@heroicons/vue/24/outline'

const activeTab = ref('account') // account | qrcode
const showPassword = ref(false)
const loading = ref(false)

const form = reactive({
  username: '',
  password: '',
  remember: false,
})

async function handleLogin() {
  loading.value = true
  try {
    // 调用登录API
    await $fetch('/api/auth/login', {
      method: 'POST',
      body: JSON.stringify({
        username: form.username,
        password: form.password,
      }),
    })
    // 登录成功后跳转
    navigateTo('/dashboard')
  }
  catch (error) {
    // 错误处理
    console.error('登录失败:', error)
  }
  finally {
    loading.value = false
  }
}
</script>

<template>
  <div
    class="
      flex min-h-screen flex-col justify-center bg-gray-50 py-12
      sm:px-6
      lg:px-8
    "
  >
    <!-- 头部LOGO -->
    <div class="sm:mx-auto sm:w-full sm:max-w-md">
      <!-- <img class="mx-auto h-16 w-auto" src="~/assets/images/logo.png" alt="三角洲行动代练平台"> -->
      <h2 class="mt-6 text-center text-3xl font-extrabold text-gray-900">
        账号登录
      </h2>
    </div>

    <!-- 登录卡片 -->
    <div
      class="
        mt-8
        sm:mx-auto sm:w-full sm:max-w-md
      "
    >
      <div
        class="
          border-t-4 border-primary bg-white px-4 py-8 shadow-md
          sm:rounded-lg sm:px-10
        "
      >
        <!-- 登录方式切换 -->
        <div class="flex border-b">
          <button
            :class="{ 'border-b-2 border-primary font-medium text-primary': activeTab === 'account' }"
            class="
              px-4 py-2
              focus:outline-none
            "
            @click="activeTab = 'account'"
          >
            账号登录
          </button>
          <button
            :class="{ 'border-b-2 border-primary font-medium text-primary': activeTab === 'qrcode' }"
            class="
              px-4 py-2
              focus:outline-none
            "
            @click="activeTab = 'qrcode'"
          >
            扫码登录
          </button>
        </div>

        <!-- 账号密码登录表单 -->
        <form v-if="activeTab === 'account'" class="mt-6 space-y-6" @submit.prevent="handleLogin">
          <div>
            <label for="username" class="block text-sm font-medium text-gray-700">账号</label>
            <div class="mt-1">
              <input
                id="username"
                v-model="form.username"
                name="username"
                type="text"
                autocomplete="username"
                required
                class="
                  block w-full appearance-none rounded-md border border-gray-300 px-3 py-2
                  placeholder-gray-400 shadow-sm
                  focus:border-primary focus:ring-primary focus:outline-none
                  sm:text-sm
                "
              >
            </div>
          </div>

          <div>
            <label for="password" class="block text-sm font-medium text-gray-700">密码</label>
            <div class="relative mt-1">
              <input
                id="password"
                v-model="form.password"
                name="password"
                :type="showPassword ? 'text' : 'password'"
                autocomplete="current-password"
                required
                class="
                  block w-full appearance-none rounded-md border border-gray-300 px-3 py-2
                  placeholder-gray-400 shadow-sm
                  focus:border-primary focus:ring-primary focus:outline-none
                  sm:text-sm
                "
              >
              <div class="absolute inset-y-0 right-0 flex items-center pr-3">
                <button
                  type="button"
                  class="
                    text-gray-500
                    hover:text-gray-700
                    focus:outline-none
                  "
                  @click="showPassword = !showPassword"
                >
                  <EyeIcon v-if="showPassword" class="h-5 w-5" />
                  <EyeOffIcon v-else class="h-5 w-5" />
                </button>
              </div>
            </div>
          </div>

          <div class="flex items-center justify-between">
            <div class="flex items-center">
              <input
                id="remember-me"
                v-model="form.remember"
                name="remember-me"
                type="checkbox"
                class="
                  h-4 w-4 rounded border-gray-300 text-primary
                  focus:ring-primary
                "
              >
              <label for="remember-me" class="ml-2 block text-sm text-gray-700">
                记住我
              </label>
            </div>

            <div class="text-sm">
              <NuxtLink
                to="/auth/forgot" class="
                  hover:text-primary-dark
                  font-medium text-primary
                "
              >
                忘记密码?
              </NuxtLink>
            </div>
          </div>

          <div>
            <button
              type="submit"
              :disabled="loading"
              class="
                hover:bg-primary-dark
                flex w-full justify-center rounded-md border border-transparent bg-primary px-4 py-2
                text-sm font-medium text-white shadow-sm transition-colors
                focus:ring-2 focus:ring-primary focus:ring-offset-2 focus:outline-none
              "
              :class="{ 'cursor-not-allowed opacity-70': loading }"
            >
              <span v-if="!loading">登录</span>
              <svg v-else class="mr-3 -ml-1 h-5 w-5 animate-spin text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4" />
                <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z" />
              </svg>
            </button>
          </div>
        </form>

        <!-- 扫码登录 -->
        <div v-if="activeTab === 'qrcode'" class="mt-6 flex flex-col items-center">
          <div class="rounded-md border bg-white p-4">
            <!-- <img src="~/assets/images/qrcode-placeholder.png" alt="扫码登录" class="h-48 w-48"> -->
            <p class="mt-2 text-center text-sm text-gray-500">
              使用三角洲行动APP扫码登录
            </p>
          </div>
          <p class="mt-4 text-sm text-gray-600">
            还没有账号? <NuxtLink
              to="/auth/register" class="
                hover:text-primary-dark
                font-medium text-primary
              "
            >
              立即注册
            </NuxtLink>
          </p>
        </div>

        <!-- 第三方登录 -->
        <div class="mt-6">
          <div class="relative">
            <div class="absolute inset-0 flex items-center">
              <div class="w-full border-t border-gray-300" />
            </div>
            <div class="relative flex justify-center text-sm">
              <span class="bg-white px-2 text-gray-500">其他登录方式</span>
            </div>
          </div>

          <div class="mt-6 grid grid-cols-3 gap-3">
            <div>
              <button
                type="button"
                class="
                  inline-flex w-full justify-center rounded-md border border-gray-300 bg-white px-4
                  py-2 text-sm font-medium text-gray-700 shadow-sm
                  hover:bg-gray-50
                  focus:ring-2 focus:ring-primary focus:ring-offset-2 focus:outline-none
                "
              >
                <span class="sr-only">使用微信登录</span>
                <!-- <img src="~/assets/icons/wechat.svg" class="h-5 w-5" alt="微信"> -->
              </button>
            </div>

            <div>
              <button
                type="button"
                class="
                  inline-flex w-full justify-center rounded-md border border-gray-300 bg-white px-4
                  py-2 text-sm font-medium text-gray-700 shadow-sm
                  hover:bg-gray-50
                  focus:ring-2 focus:ring-primary focus:ring-offset-2 focus:outline-none
                "
              >
                <span class="sr-only">使用QQ登录</span>
                <!-- <img src="~/assets/icons/qq.svg" class="h-5 w-5" alt="QQ"> -->
              </button>
            </div>

            <div>
              <button
                type="button"
                class="
                  inline-flex w-full justify-center rounded-md border border-gray-300 bg-white px-4
                  py-2 text-sm font-medium text-gray-700 shadow-sm
                  hover:bg-gray-50
                  focus:ring-2 focus:ring-primary focus:ring-offset-2 focus:outline-none
                "
              >
                <span class="sr-only">使用微博登录</span>
                <!-- <img src="~/assets/icons/weibo.svg" class="h-5 w-5" alt="微博"> -->
              </button>
            </div>
          </div>
        </div>
      </div>

      <!-- 安全认证标识 -->
      <div class="mt-6 flex items-center justify-center space-x-2 text-center text-sm text-gray-500">
        <ShieldCheckIcon class="h-4 w-4 text-green-500" />
        <span>平台已通过ISO27001信息安全管理体系认证</span>
      </div>
    </div>
  </div>
</template>

<style scoped>
.bg-primary {
  background-color: #1e40af; /* 替换为您的品牌色 */
}
.border-primary {
  border-color: #1e40af;
}
.text-primary {
  color: #1e40af;
}
.hover\:bg-primary-dark:hover {
  background-color: #1e3a8a;
}
.hover\:text-primary-dark:hover {
  color: #1e3a8a;
}
.focus\:ring-primary:focus {
  --tw-ring-color: #1e40af;
}
</style>
