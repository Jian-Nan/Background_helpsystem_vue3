<!-- 楼层新增编辑弹窗 -->
<template>
  <div class="dialog-form">
    <t-dialog
      v-model:visible="formVisible"
      :header="title + '楼层'"
      :footer="false"
      :on-close="onClickCloseBtn"
    >
      <template #body>
        <!-- 表单内容 -->
        <div class="dialogCenter">
          <t-form
            ref="form"
            :data="formData"
            :rules="rules"
            :label-width="80"
            on-cancel="onClickCloseBtn"
            :reset-type="resetType"
            @reset="onClickCloseBtn"
            @submit="onSubmit"
          >
            <t-form-item label="楼层名称：" name="name">
              <t-input
                v-model="formData.name"
                class="wt-400"
                placeholder="请输入"
                clearable
                show-limit-number
                :maxlength="5"
              >
              </t-input>
            </t-form-item>
            <t-form-item label="排序：" name="code">
              <t-input-number
                v-model="formData.code"
                :min="minNumber"
                :decimal-places="0"
                @blur="textBlurNo"
                @change="textBlurNo"
              ></t-input-number>
            </t-form-item>

            <t-form-item class="dialog-footer">
              <button class="bt bt-grey wt-60" type="reset">取消</button>
              <button
                v-if="formData.id"
                class="bt-red wt-60"
                :class="roomVoList.length > 0 ? 'bt-forbid' : ''"
                type="button"
                @click="handleDelete(formData.id)"
              >
                <span>删除</span>
              </button>
              <button theme="primary" type="submit" class="bt wt-60">
                <span>确定</span>
              </button>
            </t-form-item>
          </t-form>
        </div>
      </template>
    </t-dialog>
  </div>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue'
import { ValidateResultContext } from 'tdesign-vue-next'
import { useUserStore } from '@/store'
// 获取父组件值、方法
const props = defineProps({
  // 弹层隐藏显示
  visible: {
    type: Boolean,
    default: false
  },
  // 房间数据
  roomVoList: {
    type: Array,
    default: () => {
      return []
    }
  },
  //   详情数据
  data: {
    type: Object,
    default: () => {
      return {}
    }
  },
  // 最新值
  minNumber: {
    type: Number,
    default: 1
  },
  // 标题
  title: {
    type: String,
    default: '新增'
  }
})
// ------定义变量------
// 触发父级事件
const emit: Function = defineEmits([
  'handleClose',
  'fetchData',
  'handleAdd',
  'handleEdit',
  'handleDelete'
])
const userStore = useUserStore()
const resetType = ref('empty') // 重置表单
const form = ref() // 表单
const formVisible = ref(false) // 弹窗
// 表单数据
const formData = ref<Object | any>({
  code: 1
})
// 表单校验
const rules = {
  name: [
    {
      required: true,
      message: '楼层名称为空，请输入楼层名字',
      type: 'error',
      trigger: 'blur'
    }
  ],
  code: [
    // 排序校验
    {
      required: true,
      message: '排序为空，请输入排序',
      type: 'error',
      trigger: 'blur'
    }
  ]
}
// 弹窗标题
const title = ref()
// 监听器，监听父级传递的visible值，控制弹窗显示隐藏
watch(
  () => props.visible,
  () => {
    formVisible.value = props.visible
    title.value = props.title
  }
)
// 监听器，监听父级传递的data值，控制表单数据
watch(
  () => props.data,
  (val) => {
    formData.value.id = val.id
    formData.value.name = val.name
    formData.value.code = val.code
  }
)
// -----定义方法------
// 提交表单
const onSubmit = (result: ValidateResultContext<FormData>) => {
  if (result.validateResult === true) {
    if (props.title === '新增') {
      // 调用新增接口
      formData.value.id = ''
      emit('handleAdd', formData.value)
    } else {
      // 调用编辑接口
      emit('handleEdit', formData.value)
    }
    userStore.setFloorInfo(JSON.parse(JSON.stringify(formData.value)))
  }
}
// 清除表单数据
const handleClear = () => {
  // 重置表单
  form.value.reset()
  formData.value.code = 1
}
// 点击取消关闭
const onClickCloseBtn = () => {
  handleClear()
  emit('handleClose')
}
// 监听排序
const textBlurNo = () => {
  const data = Number(formData.value.code)
  if (data <= 1) {
    formData.value.code = 1
  }
}
// 删除
const handleDelete = (id?) => {
  // 楼层下有房间，禁止删除
  if (props.roomVoList.length === 0) {
    emit('handleDelete', id)
  }
}
// 传递给父组件的方法
defineExpose({
  handleClear
})
</script>
