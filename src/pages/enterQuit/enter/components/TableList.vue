<!-- 基础表格组件 -->
<template>
  <div class="baseList">
    <div class="tableBoxs">
      <t-table
        :data="listData"
        :columns="COLUMNS"
        :row-key="rowKey"
        vertical-align="middle"
        :hover="true"
        :loading="dataLoading"
        table-layout="fixed"
        table-content-width="100%"
      >
        <!-- 序号 -->
        <template #rowIndex="{ rowIndex }">{{ rowIndex + 1 }}</template>
        <!-- end -->
        <!-- 床位-->
        <template #bedVo="{ row }">
          {{ row.bedVo.bedNumber ? row.bedVo.bedNumber : '--' }}
        </template>
        <!-- end -->
        <!-- 入住日期-->
        <template #checkInTime="{ row }">
          {{ row.checkInTime ? getDateInfo(row.checkInTime) : '--' }}
        </template>
        <!-- end -->
        <!-- 在操作栏添加查看操作 -->
        <template #op="{ row }">
          <div class="operateCon">
            <a class="font-bt" @click="handleOpen(row)">查看</a>
          </div>
        </template>
        <!-- end -->
        <!-- 暂无数据 -->
        <template #empty>
          <NoData></NoData>
        </template>
        <!-- end -->
      </t-table>
      <!-- 分页 -->
      <t-pagination
        v-if="total > 10"
        v-model="pagination.pageNum"
        v-model:pageSize="pagination.pageSize"
        :total="total"
        @change="onPageChange"
      />
      <!-- end -->
    </div>
  </div>
</template>

<script setup lang="ts">
import { useRouter } from 'vue-router'
import { COLUMNS } from '../constants' // 表格列
import { getDateInfo } from '@/utils/date' // 时间处理
// 组件
// 赞数数据提示
import NoData from '@/components/noData/index.vue'

// 获取父级数据
const props = defineProps({
  // 列表数据
  listData: {
    type: Object,
    default: () => {
      return []
    }
  },
  // 总条数
  total: {
    type: Number,
    default: 0
  },
  // 分页
  pagination: {
    type: Object,
    default: () => ({})
  },
  // 加载状态
  dataLoading: {
    type: Boolean,
    default: false
  }
})
// ------定义变量------
const router = useRouter() // 获取全局
// 行的key
const rowKey = 'index'
const emit = defineEmits(['handleOpen', 'getCurrent'])
// 打开操作弹窗
const handleOpen = (val) => {
  router.push({
    path: `/enterQuit/enterDetails`,
    query: { assigneeId: val.applicatId, code: val.checkInCode }
  })
}
// 点击翻页
const onPageChange = (pageInfo) => {
  emit('getCurrent', pageInfo)
}
</script>
