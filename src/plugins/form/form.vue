<script setup lang="ts">
import {
  defineProps,
  provide,
  ref,
  onMounted,
  reactive,
  watch,
  defineExpose,
} from 'vue'
// 接受参数
const props = defineProps<{
  model: {
    type: Object
    required: true
  }
  rules: Object
}>()
// 注入方法
const fileds = ref<any[]>([])
const addFiled = (filed: any) => {
  filed && fileds.value.push(filed)
}
const removeFiled = (filed: any) => {
  const index = fileds.value.indexOf(filed)
  if (index > -1) {
    fileds.value.splice(index, 1)
  }
}
const doValidate = () => {
  let hasModel = !!props.model
  if (!hasModel) {
    console.error('model is required')
  }
  return hasModel
}
// 验证
const validate = (callback) => {
  if (!doValidate()) return

  let promise
  if (typeof callback !== 'function') {
    promise = new Promise((resolve, reject) => {
      callback = function (valid) {
        return valid ? resolve(valid) : reject(valid)
      }
    })
  }

  let valid = true
  let count = 0
  let invalidFields = {}
  if (fileds.value.length === 0) {
    callback(true)
  }
  fileds.value.forEach((filed) => {
    filed.validate((message, field) => {
      if (message) {
        valid = false
        // 合并对象
        invalidFields = { ...invalidFields, [field]: message }
      }
      if (++count == fileds.value.length) {
        callback(valid, invalidFields)
      }
    })
  })
  if (promise) {
    return promise
  }
}
// 重置
const resetValid = () => {
  fileds.value.forEach((filed) => {
    filed.resetField()
  })
}
defineExpose({
  validate,
  resetValid,
})
provide('elForm', {
  model: props.model,
  rules: props.rules,
  addFiled: addFiled,
  removeFiled: removeFiled,
})
onMounted(() => {
  // 注册
  // this.$on('add', (filed) => {
  //   fileds.value.push(filed)
  // })
  // // 移除
  // this.$on('remove', (filed) => {
  //   const index = fileds.value.indexOf(filed)
  //   if (index > -1) {
  //     fileds.value.splice(index, 1)
  //   }
  // })
})
</script>

<template>
  <form class="form-style">
    <slot></slot>
  </form>
</template>

<style lang="scss" scoped></style>
