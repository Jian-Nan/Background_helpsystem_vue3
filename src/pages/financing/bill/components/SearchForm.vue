<!-- 搜索框区域 -->
<template>
  <div class="formBox">
    <t-form ref="form" :model="searchData" :label-width="98">
      <t-row>
        <t-col>
          <t-form-item label="账单编号：" name="billNo">
            <t-input
              v-model="searchData.billNo"
              class="form-item-content"
              type="search"
              placeholder="请输入"
              clearable
              @clear="handleClear('billNo')"
            />
          </t-form-item>
        </t-col>
        <t-col>
          <t-form-item label="老人姓名：" name="elderName">
            <t-input
              v-model="searchData.elderName"
              class="form-item-content"
              type="search"
              placeholder="请输入"
              clearable
              @clear="handleClear('elderName')"
            />
          </t-form-item>
        </t-col>
        <t-col>
          <t-form-item label="老人身份证号：" name="elderIdCard">
            <t-input
              v-model="searchData.elderIdCard"
              class="form-item-content"
              type="search"
              placeholder="请输入"
              clearable
              @clear="handleClear('elderIdCard')"
            />
          </t-form-item>
        </t-col>
        <!-- 按钮区域 -->
        <t-col class="searchBtn">
          <button type="button" class="bt-grey wt-60" @click="handleReset()">
            重置
          </button>
          <button type="button" class="bt wt-60" @click="handleSearch()">
            搜索
          </button>
        </t-col>
      </t-row>
    </t-form>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
// 获取父组件值、方法
const props = defineProps({
  // 搜索对象
  searchData: {
    type: Object,
    default: () => ({})
  }
})
// 触发父组件的方法
const emit: Function = defineEmits([
  'handleSearch',
  'handleReset',
  'handleClear'
])
const form = ref(null)
const timeData = ref([]) // 时间选择
// 重置表单
const handleReset = () => {
  timeData.value = []
  form.value.reset()
  emit('handleReset')
}
// 搜索表单
const handleSearch = () => {
  emit('handleSearch', timeData.value)
}
// 清空
const handleClear = (v) => {
  emit('handleClear', v)
}
</script>
