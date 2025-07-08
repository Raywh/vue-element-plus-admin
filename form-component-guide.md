# Form 组件使用指南

这是一个基于 Vue 3 + Element Plus + TypeScript 的高级表单组件使用指南。

## 概述

Form 组件是一个功能强大的表单生成器，支持多种表单控件类型，具有动态配置、验证、远程数据加载等特性。

## 基本用法

### 1. 引入组件和类型

```typescript
import { Form, FormSchema } from '@/components/Form'
import { useForm } from '@/hooks/web/useForm'
import { reactive } from 'vue'
```

### 2. 定义表单配置

```typescript
const schema = reactive<FormSchema[]>([
  {
    field: 'username',
    label: '用户名',
    component: 'Input',
    formItemProps: {
      rules: [
        { required: true, message: '请输入用户名', trigger: 'blur' }
      ]
    }
  },
  {
    field: 'email',
    label: '邮箱',
    component: 'Input',
    componentProps: {
      type: 'email',
      placeholder: '请输入邮箱地址'
    }
  }
])
```

### 3. 使用表单组件

```vue
<template>
  <Form :schema="schema" @register="formRegister" />
</template>

<script setup lang="ts">
const { formRegister, formMethods } = useForm()
const { getFormData } = formMethods
</script>
```

## 支持的组件类型

### 输入类组件

#### Input - 输入框
```typescript
{
  field: 'input',
  label: '输入框',
  component: 'Input',
  componentProps: {
    placeholder: '请输入内容',
    clearable: true,
    maxlength: 50
  }
}
```

#### InputNumber - 数字输入框
```typescript
{
  field: 'number',
  label: '数字输入框',
  component: 'InputNumber',
  componentProps: {
    min: 0,
    max: 100,
    step: 1
  }
}
```

#### InputPassword - 密码输入框
```typescript
{
  field: 'password',
  label: '密码',
  component: 'InputPassword',
  componentProps: {
    strength: true // 显示密码强度
  }
}
```

#### Autocomplete - 自动完成
```typescript
{
  field: 'autocomplete',
  label: '自动完成',
  component: 'Autocomplete',
  componentProps: {
    fetchSuggestions: (queryString, cb) => {
      // 返回建议列表
    }
  }
}
```

### 选择类组件

#### Select - 选择器
```typescript
{
  field: 'select',
  label: '选择器',
  component: 'Select',
  componentProps: {
    options: [
      { label: '选项1', value: '1' },
      { label: '选项2', value: '2' }
    ]
  }
}
```

#### Cascader - 级联选择器
```typescript
{
  field: 'cascader',
  label: '级联选择',
  component: 'Cascader',
  componentProps: {
    options: [
      {
        value: 'zhejiang',
        label: '浙江',
        children: [
          {
            value: 'hangzhou',
            label: '杭州'
          }
        ]
      }
    ]
  }
}
```

#### TreeSelect - 树形选择
```typescript
{
  field: 'treeSelect',
  label: '树形选择',
  component: 'TreeSelect',
  componentProps: {
    data: [
      {
        value: '1',
        label: 'Level one 1',
        children: [
          {
            value: '1-1',
            label: 'Level two 1-1'
          }
        ]
      }
    ]
  }
}
```

### 单选/多选组件

#### RadioGroup - 单选组
```typescript
{
  field: 'radio',
  label: '单选',
  component: 'RadioGroup',
  componentProps: {
    options: [
      { label: '选项1', value: '1' },
      { label: '选项2', value: '2' }
    ]
  }
}
```

#### CheckboxGroup - 多选组
```typescript
{
  field: 'checkbox',
  label: '多选',
  component: 'CheckboxGroup',
  componentProps: {
    options: [
      { label: '选项1', value: '1' },
      { label: '选项2', value: '2' }
    ]
  }
}
```

### 日期时间组件

#### DatePicker - 日期选择器
```typescript
{
  field: 'date',
  label: '日期选择',
  component: 'DatePicker',
  componentProps: {
    type: 'date',
    placeholder: '请选择日期'
  }
}
```

#### TimePicker - 时间选择器
```typescript
{
  field: 'time',
  label: '时间选择',
  component: 'TimePicker',
  componentProps: {
    placeholder: '请选择时间'
  }
}
```

#### TimeSelect - 时间选择
```typescript
{
  field: 'timeSelect',
  label: '时间选择',
  component: 'TimeSelect',
  componentProps: {
    start: '08:00',
    end: '18:00',
    step: '00:15'
  }
}
```

### 其他组件

#### Switch - 开关
```typescript
{
  field: 'switch',
  label: '开关',
  component: 'Switch',
  componentProps: {
    activeText: '开启',
    inactiveText: '关闭'
  }
}
```

#### Rate - 评分
```typescript
{
  field: 'rate',
  label: '评分',
  component: 'Rate',
  componentProps: {
    max: 5,
    allowHalf: true
  }
}
```

#### ColorPicker - 颜色选择器
```typescript
{
  field: 'color',
  label: '颜色选择',
  component: 'ColorPicker'
}
```

#### Upload - 上传
```typescript
{
  field: 'upload',
  label: '文件上传',
  component: 'Upload',
  componentProps: {
    action: '/api/upload',
    listType: 'picture-card',
    multiple: true
  }
}
```

## 表单配置详解

### FormSchema 接口

```typescript
interface FormSchema {
  field: string                    // 字段名（必填）
  label?: string                   // 标签文本
  component?: ComponentName        // 组件类型
  value?: any                      // 初始值
  componentProps?: any             // 组件属性
  formItemProps?: FormItemProps    // 表单项属性
  colProps?: ColProps              // 列属性
  hidden?: boolean                 // 是否隐藏（v-show）
  remove?: boolean                 // 是否移除（v-if）
  optionApi?: Function             // 远程加载选项
}
```

### 表单验证

```typescript
{
  field: 'username',
  label: '用户名',
  component: 'Input',
  formItemProps: {
    rules: [
      { required: true, message: '请输入用户名', trigger: 'blur' },
      { min: 3, max: 15, message: '长度在 3 到 15 个字符', trigger: 'blur' }
    ]
  }
}
```

### 动态远程数据

```typescript
{
  field: 'remoteSelect',
  label: '远程选择',
  component: 'Select',
  optionApi: async () => {
    const res = await api.getOptions()
    return res.data
  }
}
```

## 表单操作方法

### 使用 useForm Hook

```typescript
const { formRegister, formMethods } = useForm()
const {
  setValues,      // 设置表单值
  getFormData,    // 获取表单数据
  setProps,       // 设置表单属性
  delSchema,      // 删除表单项
  addSchema,      // 添加表单项
  setSchema,      // 设置表单项属性
  getComponentExpose,  // 获取组件实例
  getFormItemExpose,   // 获取表单项实例
  getElFormExpose      // 获取 Element Plus 表单实例
} = formMethods
```

### 常用操作示例

#### 设置表单值
```typescript
const setFormData = () => {
  setValues({
    username: 'admin',
    email: 'admin@example.com',
    age: 25
  })
}
```

#### 获取表单数据
```typescript
const getFormValues = async () => {
  const data = await getFormData()
  console.log(data)
}
```

#### 表单验证
```typescript
const validate = async () => {
  const elFormExpose = await getElFormExpose()
  elFormExpose?.validate((valid) => {
    if (valid) {
      console.log('验证通过')
    } else {
      console.log('验证失败')
    }
  })
}
```

#### 重置表单
```typescript
const resetForm = async () => {
  const elFormExpose = await getElFormExpose()
  elFormExpose?.resetFields()
}
```

#### 动态添加表单项
```typescript
const addFormItem = () => {
  addSchema({
    field: 'newField',
    label: '新字段',
    component: 'Input'
  })
}
```

#### 动态删除表单项
```typescript
const deleteFormItem = () => {
  delSchema('fieldName')
}
```

#### 修改表单项属性
```typescript
const updateFormItem = () => {
  setSchema([
    {
      field: 'username',
      path: 'componentProps.disabled',
      value: true
    }
  ])
}
```

## 事件处理

### 表单验证事件
```vue
<template>
  <Form 
    :schema="schema" 
    @register="formRegister"
    @validate="onValidate"
  />
</template>

<script setup lang="ts">
const onValidate = (prop: string, isValid: boolean, message: string) => {
  console.log('验证事件:', prop, isValid, message)
}
</script>
```

### 组件事件处理
```typescript
{
  field: 'input',
  label: '输入框',
  component: 'Input',
  componentProps: {
    on: {
      change: (value) => {
        console.log('输入值改变:', value)
      },
      focus: () => {
        console.log('获得焦点')
      },
      blur: () => {
        console.log('失去焦点')
      }
    }
  }
}
```

## 布局配置

### 响应式布局
```typescript
{
  field: 'responsive',
  label: '响应式',
  component: 'Input',
  colProps: {
    span: 24,
    xs: 24,
    sm: 12,
    md: 8,
    lg: 6,
    xl: 4
  }
}
```

### 表单全局配置
```typescript
const formProps = {
  labelWidth: '120px',
  size: 'default',
  disabled: false,
  inline: false,
  labelPosition: 'right'
}
```

## 样式定制

### 组件样式
```typescript
{
  field: 'styled',
  label: '自定义样式',
  component: 'Input',
  componentProps: {
    style: {
      width: '200px',
      backgroundColor: '#f5f5f5'
    }
  }
}
```

### 表单项样式
```typescript
{
  field: 'styledItem',
  label: '自定义表单项',
  component: 'Input',
  formItemProps: {
    style: {
      marginBottom: '20px'
    }
  }
}
```

## 完整示例

```vue
<template>
  <div>
    <!-- 操作按钮 -->
    <div class="form-controls">
      <el-button @click="setFormData">设置数据</el-button>
      <el-button @click="getFormValues">获取数据</el-button>
      <el-button @click="validate">验证表单</el-button>
      <el-button @click="resetForm">重置表单</el-button>
    </div>

    <!-- 表单组件 -->
    <Form 
      :schema="schema" 
      @register="formRegister"
      @validate="onValidate"
    />
  </div>
</template>

<script setup lang="ts">
import { Form, FormSchema } from '@/components/Form'
import { useForm } from '@/hooks/web/useForm'
import { reactive } from 'vue'
import { useValidator } from '@/hooks/web/useValidator'

const { required } = useValidator()

const schema = reactive<FormSchema[]>([
  {
    field: 'username',
    label: '用户名',
    component: 'Input',
    formItemProps: {
      rules: [required()]
    }
  },
  {
    field: 'email',
    label: '邮箱',
    component: 'Input',
    componentProps: {
      type: 'email',
      placeholder: '请输入邮箱'
    }
  },
  {
    field: 'age',
    label: '年龄',
    component: 'InputNumber',
    componentProps: {
      min: 0,
      max: 120
    }
  },
  {
    field: 'gender',
    label: '性别',
    component: 'RadioGroup',
    componentProps: {
      options: [
        { label: '男', value: 'male' },
        { label: '女', value: 'female' }
      ]
    }
  },
  {
    field: 'hobbies',
    label: '爱好',
    component: 'CheckboxGroup',
    componentProps: {
      options: [
        { label: '读书', value: 'reading' },
        { label: '运动', value: 'sports' },
        { label: '音乐', value: 'music' }
      ]
    }
  }
])

const { formRegister, formMethods } = useForm()
const { setValues, getFormData, getElFormExpose } = formMethods

const setFormData = () => {
  setValues({
    username: 'admin',
    email: 'admin@example.com',
    age: 25,
    gender: 'male',
    hobbies: ['reading', 'sports']
  })
}

const getFormValues = async () => {
  const data = await getFormData()
  console.log('表单数据:', data)
}

const validate = async () => {
  const elFormExpose = await getElFormExpose()
  elFormExpose?.validate((valid) => {
    if (valid) {
      console.log('验证通过')
    } else {
      console.log('验证失败')
    }
  })
}

const resetForm = async () => {
  const elFormExpose = await getElFormExpose()
  elFormExpose?.resetFields()
}

const onValidate = (prop: string, isValid: boolean, message: string) => {
  console.log('验证事件:', prop, isValid, message)
}
</script>

<style scoped>
.form-controls {
  margin-bottom: 20px;
}

.form-controls .el-button {
  margin-right: 10px;
}
</style>
```

## 注意事项

1. **响应式数据**: 使用 `reactive` 包装 schema 数组以支持动态修改
2. **表单验证**: 使用 `useValidator` 获取常用验证规则
3. **异步操作**: 获取表单数据和验证是异步操作，需要使用 `await`
4. **组件实例**: 通过 `getComponentExpose` 可以获取具体组件的实例进行更精细的操作
5. **事件绑定**: 在 `componentProps.on` 中绑定事件处理函数
6. **远程数据**: 使用 `optionApi` 属性可以实现远程数据加载

这个表单组件提供了丰富的功能和灵活的配置选项，可以满足大多数表单开发需求。