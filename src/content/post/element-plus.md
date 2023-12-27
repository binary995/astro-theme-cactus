---
title: "Element-plus补充"
publishDate: "2023 12 27"
description: "An example post for Astro Cactus, detailing how to add a custom social image card in the frontmatter"
tags: ["vue3", "blog", "element"]
ogImage: "/social-card.png"
---


## 表单验证-Eg

```js
//表单输入的规则
const rules = {
  userName: [
    { required: true, message: '请输入用户名', trigger: 'blur' },
    { min: 5, max: 10, message: '长度在 5 到 10 个字符', trigger: 'blur' }
  ],
  passWord: [
    { required: true, message: '请输入密码', trigger: 'blur' },
    {
      pattern: /^\S{6,15}$/,
      message: '密码必须是 6-15 位的非空字符串',
      trigger: 'blur'
    }
  ],
  repassWord: [
    { required: true, message: '请再次输入密码', trigger: 'blur' },
    {
      pattern: /^\S{6,15}$/,
      message: '密码必须是 6-15 位的非空字符串',
      trigger: 'blur'
    },
    {
      validator: (rule, value, callback) => {
        //判断 value 和当前form 中收集的 passWord 是否相同
        if (value !== formModel.value.passWord) {
          callback(new Error('两次输入的密码不一致'))
        } else {
          callback() //就算校验成功也要callback
        }
      }
    }
  ]
}
```

**校验步骤**

1. 绑定整个from的数据对象
2. 绑定整个rules规则对象
3. 给表单元素,绑定from的子属性
4. 在对应代码块中使用props配置生效的是哪个校验规则



**整个表单校验规则**

- 非空校验  required:true  ,  message消息提示  , trigger触发校验的时机  blur  change

- 长度校验  min:xx    max:xx

- 正则校验  pattern : 正则规则

- 自定义校验  validator   自己写逻辑规则校验(校验函数)

  

**绑定校验**

```js
<el-form-item prop="username
```

将验证规则和`usename`进行绑定









## 时间格式化





1. 新建 `utils/format.js` 封装格式化日期函数	

   ```js
   import { dayjs } from 'element-plus'
   
   export const formatTime = (time) => dayjs(time).format('YYYY年MM月DD日')
   ```

2. 导入使用

   ```js
   import { formatTime } from '@/utils/format'
   
   <el-table-column label="发表时间">
     <template #default="{ row }">
       {{ formatTime(row.pub_date) }}
     </template>
   </el-table-column>
   ```

   





## Empty空状态使用

```js
<template #empty>
    <el-empty description="description" />
</template>
```

- 想要实现`el-table`没有数据时,实现占位效果,需要使用插槽配合使用





## 获得table按键点击的数据

```js
<el-table :data="channelList" style="width: 100%">
  <el-table-column label="序号" width="100" type="index"> </el-table-column>
  <el-table-column label="分类名称" prop="cate_name"></el-table-column>
  <el-table-column label="分类别名" prop="cate_alias"></el-table-column>
  <el-table-column label="操作" width="100">
    <template #default="{ row }">
      <el-button
        :icon="Edit"
        circle
        plain
        type="primary"
        @click="onEditChannel(row)"
      ></el-button>
      <el-button
        :icon="Delete"
        circle
        plain
        type="danger"
        @click="onDelChannel(row)"
      ></el-button>
    </template>
  </el-table-column>
  <template #empty>
    <el-empty description="没有数据" />
  </template>
</el-table>


const onEditChannel = (row) => {
  console.log(row)
}
const onDelChannel = (row) => {
  console.log(row)
}
```

> 使用row来获得

