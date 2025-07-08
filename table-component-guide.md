# Table 组件使用指南

这是一个基于 Vue 3 + Element Plus + TypeScript 的高级表格组件使用指南。

## 概述

Table 组件是一个功能强大的表格组件，支持数据展示、分页、排序、筛选、树形表格、图片预览、视频预览等特性。

## 基本用法

### 1. 引入组件和类型

```typescript
import { Table, TableColumn } from '@/components/Table'
import { useTable } from '@/hooks/web/useTable'
import { reactive, ref } from 'vue'
```

### 2. 简单表格

```vue
<template>
  <Table
    :columns="columns"
    :data="tableData"
    :loading="loading"
  />
</template>

<script setup lang="ts">
import { Table, TableColumn } from '@/components/Table'
import { ref } from 'vue'

// 定义列配置
const columns: TableColumn[] = [
  {
    field: 'name',
    label: '姓名'
  },
  {
    field: 'age',
    label: '年龄'
  },
  {
    field: 'address',
    label: '地址'
  }
]

// 表格数据
const tableData = ref([
  { name: '张三', age: 18, address: '北京市' },
  { name: '李四', age: 20, address: '上海市' },
  { name: '王五', age: 22, address: '广州市' }
])

const loading = ref(false)
</script>
```

### 3. 使用 useTable Hook

```vue
<template>
  <Table
    v-model:pageSize="pageSize"
    v-model:currentPage="currentPage"
    :columns="columns"
    :data="dataList"
    :loading="loading"
    :pagination="{
      total: total
    }"
    @register="tableRegister"
  />
</template>

<script setup lang="ts">
import { Table, TableColumn } from '@/components/Table'
import { useTable } from '@/hooks/web/useTable'
import { getTableListApi } from '@/api/table'

// 列配置
const columns: TableColumn[] = [
  {
    field: 'title',
    label: '标题'
  },
  {
    field: 'author',
    label: '作者'
  },
  {
    field: 'display_time',
    label: '显示时间'
  }
]

// 使用 useTable Hook
const { tableRegister, tableMethods, tableState } = useTable({
  fetchDataApi: async () => {
    const { currentPage, pageSize } = tableState
    const res = await getTableListApi({
      pageIndex: currentPage.value,
      pageSize: pageSize.value
    })
    return {
      list: res.data.list,
      total: res.data.total
    }
  }
})

const { loading, dataList, total, currentPage, pageSize } = tableState
</script>
```

## 列配置详解

### TableColumn 接口

```typescript
interface TableColumn {
  field: string                    // 字段名（必填）
  label?: string                   // 列标题
  type?: string                    // 列类型：selection、index、expand等
  width?: string | number          // 列宽度
  minWidth?: string | number       // 最小宽度
  fixed?: boolean | 'left' | 'right'  // 固定列
  align?: 'left' | 'center' | 'right'  // 对齐方式
  headerAlign?: 'left' | 'center' | 'right'  // 表头对齐方式
  sortable?: boolean               // 是否可排序
  formatter?: Function             // 格式化函数
  showOverflowTooltip?: boolean    // 是否显示溢出提示
  hidden?: boolean                 // 是否隐藏
  children?: TableColumn[]         // 多级表头
  slots?: {                        // 自定义插槽
    default?: Function
    header?: Function
  }
}
```

## 列类型

### 1. 基本列

```typescript
{
  field: 'name',
  label: '姓名',
  width: 120,
  align: 'center'
}
```

### 2. 索引列

```typescript
{
  field: 'index',
  label: '序号',
  type: 'index',
  width: 80
}
```

### 3. 选择列

```typescript
{
  field: 'selection',
  type: 'selection',
  width: 50,
  reserveSelection: true  // 保留选择状态
}
```

### 4. 展开列

```typescript
{
  field: 'expand',
  type: 'expand',
  slots: {
    default: (data: TableSlotDefault) => {
      const { row } = data
      return (
        <div>
          <p>详细信息：{row.detail}</p>
        </div>
      )
    }
  }
}
```

### 5. 自定义列

```typescript
{
  field: 'status',
  label: '状态',
  formatter: (row, column, cellValue) => {
    return cellValue === 1 ? '启用' : '禁用'
  }
}
```

### 6. 操作列

```typescript
{
  field: 'action',
  label: '操作',
  slots: {
    default: (data) => {
      return (
        <div>
          <el-button type="primary" size="small" onClick={() => edit(data)}>
            编辑
          </el-button>
          <el-button type="danger" size="small" onClick={() => del(data)}>
            删除
          </el-button>
        </div>
      )
    }
  }
}
```

## 特殊功能

### 1. 多级表头

```typescript
const columns: TableColumn[] = [
  {
    field: 'name',
    label: '姓名'
  },
  {
    field: 'info',
    label: '基本信息',
    children: [
      {
        field: 'age',
        label: '年龄'
      },
      {
        field: 'address',
        label: '地址'
      }
    ]
  }
]
```

### 2. 排序功能

```typescript
{
  field: 'createTime',
  label: '创建时间',
  sortable: true,
  sortMethod: (a, b) => {
    return new Date(a.createTime) - new Date(b.createTime)
  }
}
```

### 3. 固定列

```typescript
{
  field: 'name',
  label: '姓名',
  fixed: 'left',
  width: 120
},
{
  field: 'action',
  label: '操作',
  fixed: 'right',
  width: 200
}
```

### 4. 图片预览

```vue
<template>
  <Table
    :columns="columns"
    :data="tableData"
    :image-preview="['image_uri']"
  />
</template>

<script setup lang="ts">
const columns: TableColumn[] = [
  {
    field: 'title',
    label: '标题'
  },
  {
    field: 'image_uri',
    label: '图片预览'
  }
]
</script>
```

### 5. 视频预览

```vue
<template>
  <Table
    :columns="columns"
    :data="tableData"
    :video-preview="['video_uri']"
  />
</template>

<script setup lang="ts">
const columns: TableColumn[] = [
  {
    field: 'title',
    label: '标题'
  },
  {
    field: 'video_uri',
    label: '视频预览'
  }
]
</script>
```

### 6. 树形表格

```vue
<template>
  <Table
    :columns="columns"
    :data="treeData"
    row-key="id"
    default-expand-all
    :tree-props="{ children: 'children', hasChildren: 'hasChildren' }"
  />
</template>

<script setup lang="ts">
const treeData = ref([
  {
    id: 1,
    name: '一级菜单',
    children: [
      {
        id: 2,
        name: '二级菜单'
      }
    ]
  }
])
</script>
```

## 分页配置

### 基本分页

```vue
<template>
  <Table
    v-model:current-page="currentPage"
    v-model:page-size="pageSize"
    :columns="columns"
    :data="tableData"
    :pagination="{
      total: total,
      pageSizes: [10, 20, 50, 100],
      layout: 'total, sizes, prev, pager, next, jumper'
    }"
  />
</template>

<script setup lang="ts">
const currentPage = ref(1)
const pageSize = ref(10)
const total = ref(0)
</script>
```

### 分页配置项

```typescript
interface Pagination {
  total?: number              // 总条数
  pageSize?: number           // 每页显示条数
  currentPage?: number        // 当前页数
  pageSizes?: number[]        // 每页显示个数选择器的选项
  layout?: string             // 组件布局
  background?: boolean        // 是否为分页按钮添加背景色
  small?: boolean             // 是否使用小型分页样式
  hideOnSinglePage?: boolean  // 只有一页时是否隐藏
}
```

## 表格操作方法

### 使用 useTable Hook

```typescript
const { tableRegister, tableMethods, tableState } = useTable({
  fetchDataApi: async () => {
    // 获取数据的API
    const res = await getTableListApi(params)
    return {
      list: res.data.list,
      total: res.data.total
    }
  },
  fetchDelApi: async () => {
    // 删除数据的API
    const res = await deleteApi(ids)
    return res.success
  }
})

const {
  setProps,           // 设置表格属性
  setColumn,          // 设置列属性
  addColumn,          // 添加列
  delColumn,          // 删除列
  getElTableExpose,   // 获取Element Plus表格实例
  refresh,            // 刷新表格数据
  delList             // 删除数据
} = tableMethods

const { loading, dataList, total, currentPage, pageSize } = tableState
```

### 常用操作示例

#### 动态设置表格属性

```typescript
// 设置表格条纹
const setStripe = () => {
  setProps({
    stripe: true
  })
}

// 设置表格高度
const setHeight = () => {
  setProps({
    height: 400
  })
}

// 设置表格边框
const setBorder = () => {
  setProps({
    border: true
  })
}
```

#### 动态操作列

```typescript
// 隐藏/显示列
const toggleColumn = (field: string, hidden: boolean) => {
  setColumn([
    {
      field: field,
      path: 'hidden',
      value: hidden
    }
  ])
}

// 修改列标题
const changeColumnLabel = (field: string, label: string) => {
  setColumn([
    {
      field: field,
      path: 'label',
      value: label
    }
  ])
}

// 添加新列
const addNewColumn = () => {
  addColumn({
    field: 'newField',
    label: '新列',
    width: 120
  })
}

// 删除列
const deleteColumn = (field: string) => {
  delColumn(field)
}
```

#### 获取选中行

```typescript
const getSelections = async () => {
  const elTableRef = await getElTableExpose()
  const selections = elTableRef?.getSelectionRows()
  console.log('选中的行:', selections)
}
```

#### 全选/取消全选

```typescript
const toggleAllSelection = async () => {
  const elTableRef = await getElTableExpose()
  elTableRef?.toggleAllSelection()
}
```

#### 清空排序

```typescript
const clearSort = async () => {
  const elTableRef = await getElTableExpose()
  elTableRef?.clearSort()
}
```

## 事件处理

### 表格事件

```vue
<template>
  <Table
    :columns="columns"
    :data="tableData"
    @selection-change="handleSelectionChange"
    @sort-change="handleSortChange"
    @row-click="handleRowClick"
    @refresh="handleRefresh"
  />
</template>

<script setup lang="ts">
// 选择改变事件
const handleSelectionChange = (selection: any[]) => {
  console.log('选中的行:', selection)
}

// 排序改变事件
const handleSortChange = ({ column, prop, order }) => {
  console.log('排序改变:', column, prop, order)
}

// 行点击事件
const handleRowClick = (row: any, column: any, event: Event) => {
  console.log('行点击:', row)
}

// 刷新事件
const handleRefresh = () => {
  console.log('刷新表格')
}
</script>
```

## 样式定制

### 表格样式

```vue
<template>
  <Table
    :columns="columns"
    :data="tableData"
    stripe
    border
    :header-cell-style="{ background: '#f5f7fa' }"
    :row-class-name="getRowClassName"
  />
</template>

<script setup lang="ts">
// 自定义行类名
const getRowClassName = ({ row, rowIndex }) => {
  if (rowIndex % 2 === 0) {
    return 'even-row'
  }
  return 'odd-row'
}
</script>

<style scoped>
:deep(.even-row) {
  background-color: #f9f9f9;
}

:deep(.odd-row) {
  background-color: #ffffff;
}
</style>
```

### 列样式

```typescript
{
  field: 'status',
  label: '状态',
  className: 'status-column',
  headerAlign: 'center',
  align: 'center',
  formatter: (row, column, cellValue) => {
    return cellValue === 1 ? '启用' : '禁用'
  }
}
```

## 完整示例

```vue
<template>
  <div>
    <!-- 操作按钮 -->
    <div class="table-operations">
      <el-button type="primary" @click="handleAdd">新增</el-button>
      <el-button type="danger" @click="handleBatchDelete">批量删除</el-button>
      <el-button @click="handleRefresh">刷新</el-button>
    </div>

    <!-- 表格 -->
    <Table
      v-model:current-page="currentPage"
      v-model:page-size="pageSize"
      :columns="columns"
      :data="dataList"
      :loading="loading"
      :pagination="{
        total: total,
        pageSizes: [10, 20, 50, 100],
        layout: 'total, sizes, prev, pager, next, jumper'
      }"
      stripe
      border
      show-summary
      @register="tableRegister"
      @selection-change="handleSelectionChange"
      @refresh="handleRefresh"
    />
  </div>
</template>

<script setup lang="tsx">
import { Table, TableColumn, TableSlotDefault } from '@/components/Table'
import { useTable } from '@/hooks/web/useTable'
import { getTableListApi, deleteTableApi } from '@/api/table'
import { ref, reactive } from 'vue'
import { ElMessage, ElMessageBox, ElTag } from 'element-plus'

// 列配置
const columns = reactive<TableColumn[]>([
  {
    field: 'selection',
    type: 'selection',
    width: 50
  },
  {
    field: 'index',
    label: '序号',
    type: 'index',
    width: 80
  },
  {
    field: 'title',
    label: '标题',
    minWidth: 120,
    showOverflowTooltip: true
  },
  {
    field: 'author',
    label: '作者',
    width: 100
  },
  {
    field: 'status',
    label: '状态',
    width: 100,
    formatter: (row, column, cellValue) => {
      return (
        <ElTag type={cellValue === 1 ? 'success' : 'danger'}>
          {cellValue === 1 ? '启用' : '禁用'}
        </ElTag>
      )
    }
  },
  {
    field: 'createTime',
    label: '创建时间',
    width: 180,
    sortable: true
  },
  {
    field: 'action',
    label: '操作',
    width: 200,
    fixed: 'right',
    slots: {
      default: (data: TableSlotDefault) => {
        const { row } = data
        return (
          <div>
            <el-button type="primary" size="small" onClick={() => handleEdit(row)}>
              编辑
            </el-button>
            <el-button type="danger" size="small" onClick={() => handleDelete(row)}>
              删除
            </el-button>
          </div>
        )
      }
    }
  }
])

// 使用 useTable Hook
const { tableRegister, tableMethods, tableState } = useTable({
  fetchDataApi: async () => {
    const { currentPage, pageSize } = tableState
    const res = await getTableListApi({
      pageIndex: currentPage.value,
      pageSize: pageSize.value
    })
    return {
      list: res.data.list,
      total: res.data.total
    }
  },
  fetchDelApi: async () => {
    const res = await deleteTableApi(selectedIds.value)
    return res.success
  }
})

const { loading, dataList, total, currentPage, pageSize } = tableState
const { refresh, delList } = tableMethods

// 选中的行
const selectedRows = ref<any[]>([])
const selectedIds = ref<number[]>([])

// 处理选择改变
const handleSelectionChange = (selection: any[]) => {
  selectedRows.value = selection
  selectedIds.value = selection.map(item => item.id)
}

// 新增
const handleAdd = () => {
  console.log('新增')
}

// 编辑
const handleEdit = (row: any) => {
  console.log('编辑:', row)
}

// 删除单行
const handleDelete = (row: any) => {
  ElMessageBox.confirm('确定要删除这条数据吗？', '提示', {
    confirmButtonText: '确定',
    cancelButtonText: '取消',
    type: 'warning'
  }).then(async () => {
    // 执行删除操作
    const res = await deleteTableApi([row.id])
    if (res.success) {
      ElMessage.success('删除成功')
      refresh()
    }
  })
}

// 批量删除
const handleBatchDelete = () => {
  if (selectedIds.value.length === 0) {
    ElMessage.warning('请先选择要删除的数据')
    return
  }
  delList(selectedIds.value.length)
}

// 刷新
const handleRefresh = () => {
  refresh()
}
</script>

<style scoped>
.table-operations {
  margin-bottom: 20px;
}

.table-operations .el-button {
  margin-right: 10px;
}
</style>
```

## 高级功能

### 1. 自定义表格汇总

```vue
<template>
  <Table
    :columns="columns"
    :data="tableData"
    show-summary
    :summary-method="getSummaries"
  />
</template>

<script setup lang="ts">
const getSummaries = (param: any) => {
  const { columns, data } = param
  const sums: string[] = []
  columns.forEach((column: any, index: number) => {
    if (index === 0) {
      sums[index] = '合计'
      return
    }
    const values = data.map((item: any) => Number(item[column.property]))
    if (!values.every((value: any) => isNaN(value))) {
      sums[index] = values.reduce((prev: any, curr: any) => {
        const value = Number(curr)
        if (!isNaN(value)) {
          return prev + curr
        } else {
          return prev
        }
      }, 0)
    } else {
      sums[index] = 'N/A'
    }
  })
  return sums
}
</script>
```

### 2. 可拖拽排序

```vue
<template>
  <Table
    :columns="columns"
    :data="tableData"
    row-key="id"
    :sortable="true"
    @sort-change="handleSortChange"
  />
</template>

<script setup lang="ts">
const handleSortChange = (data: any) => {
  console.log('排序改变:', data)
}
</script>
```

### 3. 自定义空状态

```vue
<template>
  <Table
    :columns="columns"
    :data="tableData"
    :empty-text="'暂无数据'"
  />
</template>
```

## 注意事项

1. **数据绑定**: 使用 `v-model` 绑定分页参数以支持双向数据绑定
2. **异步操作**: 使用 `useTable` Hook 处理异步数据加载
3. **性能优化**: 对于大量数据，考虑使用虚拟滚动或分页
4. **响应式**: 列配置使用 `reactive` 以支持动态修改
5. **类型安全**: 使用 TypeScript 类型定义确保类型安全
6. **事件处理**: 合理使用表格事件处理用户交互
7. **样式定制**: 通过 CSS 和组件属性自定义表格样式

这个 Table 组件提供了丰富的功能和灵活的配置选项，可以满足各种复杂的表格需求。