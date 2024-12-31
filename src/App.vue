<script setup>
// import HelloWorld from './components/HelloWorld.vue'
import Form from './plugins/form/form.vue'
import FormItem from './plugins/form/formItem.vue'
import { ref } from 'vue'
const form = ref({
  username: '',
})
const formRef = ref('')
const submit = function () {
  formRef.value.validate((valid, field) => {
    if (valid) {
      console.log('验证通过', field)
    } else {
      console.log('验证不通过', field)
    }
  })
}
const reset = function () {
  formRef.value.resetValid()
}
const rules = {
  username: [
    {
      required: true,
      message: '请输入用户名',
      cb: (value) => {
        return !value
      },
    },
    {
      min: 3,
      max: 6,
      message: '长度在 3 到 6 个字符',
      cb: (value) => {
        return value.length < 3 || value.length > 6
      },
    },
  ],
}
// const formInline = ref({
//   user: '',
// })
// const rulesE = ref({
//   user: [
//     { required: true, message: 'Please input Activity name', trigger: 'blur' },
//     { min: 3, max: 5, message: 'Length should be 3 to 5', trigger: 'blur' },
//   ],
// })
</script>

<template>
  <div>
    <Form ref="formRef" :model="form" :rules="rules">
      <FormItem label="用户名" prop="username">
        <input v-model="form.username" />
      </FormItem>
    </Form>
    <button @click="submit">提交</button>
    <button @click="reset">重置</button>
  </div>
  <!-- <el-form
    :inline="true"
    :model="formInline"
    :rules="rulesE"
    class="demo-form-inline"
  >
    <el-form-item label="Approved by" prop="user">
      <el-input v-model="formInline.user" placeholder="Approved by" />
      <input v-model="formInline.user" />
    </el-form-item>
  </el-form> -->
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
