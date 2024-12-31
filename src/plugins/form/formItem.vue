<script setup lang="ts">
import { ref, defineProps, computed, inject, onMounted } from 'vue'
const props = defineProps<{
  label: string
  prop: string
}>()
const elForm: {
  model: any
  rules: any[]
  addFiled: Function
  removeFiled: Function
} = inject('elForm')!
const filedValue = computed(() => {
  return elForm.model[props.prop]
})
const required = computed(() => {
  let rules = elForm.rules[props.prop]
  if (!rules) {
    return false
  }
  if (Array.isArray(rules)) {
    return rules.some((rule) => rule.required)
  } else {
    return rules.required
  }
})
let msg = ref('')
onMounted(() => {
  elForm.addFiled({
    validate: validate,
    resetField: resetField,
    prop: props.prop,
  })
})
const validate = (cb: Function) => {
  let rules = elForm.rules[props.prop]
  // 如果没有规则且非必填，直接返回
  if ((!rules || !rules.length) && !required.value) {
    cb()
    return true
  }
  // 如果没有值且非必填，直接返回
  if (!required.value && !filedValue.value) {
    cb()
    return true
  }
  // 遍历规则
  if (!Array.isArray(rules)) {
    rules = [rules]
  }
  let valid = true
  msg.value = ''
  for (let i = 0; i < rules.length; i++) {
    let rule = rules[i]
    if (rule.cb(filedValue.value)) {
      // 验证不通过
      valid = false
      msg.value = rule.message
      break
    }
  }
  cb(msg.value, props.prop)
  return valid
}

const resetField = () => {
  msg.value = ''
}
</script>

<template>
  <div class="form-item-style">
    <!-- 标签label -->
    <label v-if="label || $slots.label">
      <slot name="label">{{ props.label }}</slot>
    </label>
    <!-- 主体内容 -->
    <slot></slot>
    <!-- 错误信息 -->
    <span>{{ msg }}</span>
  </div>
</template>

<style lang="scss" scoped></style>
