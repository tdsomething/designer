<template>
  <div class="min-h-full flex items-center justify-center py-12 px-4 sm:px-6 lg:px-8">
    <div class="max-w-md w-full space-y-8">
      <div>
        <img class="mx-auto h-12 w-auto" src="@/assets/login.svg" alt="Workflow" />
        <h2 class="mt-6 text-center text-3xl font-extrabold text-gray-900">
          Sign in to your account
        </h2>
      </div>
      <form class="mt-8 space-y-6">
        <!-- <input type="hidden" name="remember" value="true" /> -->
        <div class="rounded-md shadow-sm -space-y-px">
          <div>
            <label class="sr-only">Email address</label>
            <input
              ref="emailRef"
              v-model="email"
              type="email"
              autocomplete="email"
              required
              class="appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-t-md focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm"
              placeholder="Email address"
            />
          </div>
          <div>
            <label class="sr-only">Password</label>
            <input
              ref="passwordRef"
              v-model="password"
              type="password"
              required
              autocomplete="current-password"
              class="appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-b-md focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm"
              placeholder="Password"
            />
          </div>
        </div>

        <div class="flex items-center justify-between">
          <div class="flex items-center">
            <input
              type="checkbox"
              class="h-4 w-4 text-indigo-600 focus:ring-indigo-500 border-gray-300 rounded"
            />
            <label class="ml-2 block text-sm text-gray-900"> Remember me </label>
          </div>

          <div class="text-sm">
            <a class="font-medium text-indigo-600 hover:text-indigo-500"> Forgot your password? </a>
          </div>
        </div>

        <div>
          <button
            class="group relative w-full flex justify-center py-2 px-4 border border-transparent text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
            type="submit"
            @click.prevent="handleClick"
          >
            <span class="absolute left-0 inset-y-0 flex items-center pl-3">
              <LockClosedIcon
                class="h-5 w-5 text-indigo-500 group-hover:text-indigo-400"
                aria-hidden="true"
              />
            </span>
            Sign in
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup lang="ts">
import { LockClosedIcon } from '@heroicons/vue/solid'
import { login } from '@/apis'
import { useMessage } from 'naive-ui'
import md5 from 'blueimp-md5'

const email = ref('')
const password = ref('')
const emailRef = ref()
const passwordRef = ref()
const router = useRouter()
const message = useMessage()

const handleClick = async () => {
  const pass =
    (emailRef.value as HTMLInputElement).reportValidity() &&
    (passwordRef.value as HTMLInputElement).reportValidity()
  if (pass) {
    const resp = await login({
      email: email.value,
      password: md5(password.value)
    })
    if (resp.code.toString().charAt(0) == '5') {
      return message.error(resp.msg)
    }
    const token = useLocalStorage('token', '')
    token.value = resp.data.token
    token.value && router.push({ path: '/' })
  }
}
</script>

<style lang="scss" scoped></style>
