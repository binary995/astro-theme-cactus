---
title: "后台文章管理项目"
publishDate: "2023 10 1"
description: "基于Vue3和element-Plus组件库实现的个人后台文章管理系统."
tags: ["vue3", "note", "project"]
ogImage: "/social-card.png"
---

## 1.准备

### 1. npm包管理工具

 

1. 安装

   ```js
   npm i -g pnpm
   ```

   ![image-20230807155840542](https://raw.githubusercontent.com/binary995/img_beg/main/Picgo/image-20230807155840542.png)

2. 创建项目

   ```js
   pnpm create vue
   ```

   





### 2.Eslint配置代码风格

配置文件  .eslintrc.cjs 

1. `prettier`风格配置 

   [Prettier 中文网 · Prettier 是一个“有态度”的代码格式化工具](https://www.prettier.cn/)

   

2. vue组件名称多单词组成





3. props结构(关闭 )

![image-20230807164751675](https://raw.githubusercontent.com/binary995/img_beg/main/Picgo/image-20230807164751675.png)







### 3.配置代码检查工具流



#### 1.基于husky的代码检查工作流

1. 初始化git仓库,执行git init

2. 初始化husky配置,,执行`pnpm dlx husky-init && pnpm install`

3. 修改`.husky/pre-commit`文件

   ```js
   #!/usr/bin/env sh
   . "$(dirname -- "$0")/_/husky.sh"
   
   pnpm lint
   ```

   这里使用的是`pnpm`.但是它是全量检查,耗时









#### 2.暂存区eslint校验

> lint-staged配置

1. 安装`lint-staged`包`  pnpm i lint-staged -D`
2. `package.json  `中配置 `lint-staged` 命令
3. `.husky/pre-commit` 文件修改

这样就只会检查当前所写的代码是否符合规范,不会全局检查

**git**

```
//初始化
git init

git add .
 
//提交
git commit -m '文件name'
```





#### 3.目录调整

1. 删除初始化文件

2. 修改剩余代码内容

3. 新增目录结构

4. 新增调整目录结构

5. 拷贝全局样式和图片,安装预处理器支持

   ```js
   pnpm add sass -D
   ```

   







### 4.路由初始化

![image-20230809155511713](https://raw.githubusercontent.com/binary995/img_beg/main/Picgo/image-20230809155511713.png)





1. 创建路由实例由 `createRouter`实现
2. 路由模式
   1. history 模式使用 createWebHistory()
   2. hash 模式使用 createWebHashHistory()
   3. 参数是基础路径,默认 /



```js
import { createRouter, createWebHistory } from 'vue-router'

const router = createRouter({
  //配置history模式
  //createWebHistory 不带#号  createWebHashHistory 带#号
  history: createWebHistory(import.meta.env.BASE_URL),
  //配置路由路径
  routes: []
})

export default router

```

- `history: createWebHistory(import.meta.env.BASE_URL),`

  的`import.meta.env.BASE_URL`是vite中的环境变量

  在`vite.config.js`中添加`base`来进行配置

  ```js
  // https://vitejs.dev/config/
  export default defineConfig({
    plugins: [vue()],
      //base进行配置
    base: '/jd',
    resolve: {
      alias: {
        '@': fileURLToPath(new URL('./src', import.meta.url))
      }
    }
  })
  ```

  配置之后,在进行路由跳转的时候,就会添加`jd`

  ```
  http://127.0.0.1:5173/jd/home
  ```

  





**在组件中使用route**



**错误用法**

```js
<script setup>
const goSearch = () => {
  this.$router.push('/search')
}
</script>

<template>
  <div>我是app</div>
  <button @click="$router.push('/home')">home</button>
  <button @click="goSearch">search</button>
</template>
```



在vue3中

```js
const goSearch = () => {
  this.$router.push('/search')
}
```

的用法是错误的,在vue3中只能创建实例再使用



**正确用法**

```js
<script setup>
import { useRouter, useRoute } from 'vue-router'

const router = useRouter()
const route = useRoute()

const goSearch = () => {
  router.push('/search')
}
</script>

<template>
  <div>我是app</div>
  <button @click="$router.push('/home')">home</button>
  <button @click="goSearch">search</button>
</template>

<style scoped></style>

```







### 5.引入Element Plus

![image-20230809164411533](https://raw.githubusercontent.com/binary995/img_beg/main/Picgo/image-20230809164411533.png)

[安装 | Element Plus (element-plus.org)](https://element-plus.org/zh-CN/guide/installation.html)

1. 安装element-ui-plue

   ```js
   # 选择一个你喜欢的包管理器
   
   # NPM
   $ npm install element-plus --save
   
   # Yarn
   $ yarn add element-plus
   
   # pnpm
   $ pnpm install element-plus
   ```

2. 安装自动导入插件

   ```js
   npm install -D unplugin-vue-components unplugin-auto-import
   ```

3. 配置代码

   ```js
   Vite#
   // vite.config.ts
   import { defineConfig } from 'vite'
   import AutoImport from 'unplugin-auto-import/vite'
   import Components from 'unplugin-vue-components/vite'
   import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'
   
   export default defineConfig({
     // ...
     plugins: [
       // ...
       AutoImport({
         resolvers: [ElementPlusResolver()],
       }),
       Components({
         resolvers: [ElementPlusResolver()],
       }),
     ],
   })
   ```

   

   



### 6.Pinia构建用户仓库和持久化

![image-20230809171818615](https://raw.githubusercontent.com/binary995/img_beg/main/Picgo/image-20230809171818615.png)

1. 构建仓库

2. 使用插件进行持久化

3. 统一管理

   - pinia 独立维护

     在store中创建index.js

     ```js
     import { createPinia } from 'pinia'
     import persist from 'pinia-plugin-persistedstate'
     
     const pinia = createPinia()
     pinia.use(persist)
     
     //将pinia暴露出去,在main.js中引用
     export default pinia
     
     ```

     

   - 仓库统一管理

     - 创建`modules`文件夹()来存放store仓库

     - 在index.js中来暴露仓库,来实现引用的简化

       store,index.js中添加

       ```js
       //将仓库进行暴露.来简化仓库引用的写法
       export * from './modules/user'
       
       ```

       





### 7.数据交互(axios)

> 　　完成ａｘｉｏｓ配置

1. 创建axios实例

   基准地址,超时时间

   

2. 请求拦截器

   携带token

   

3. 响应拦截器

   业务失败处理,摘取核心响应数据,401处理







```js
import axios from 'axios'
import { useUserStore } from '@/stores'
import { ElMessage } from 'element-plus'
import router from '@/router'
const baseURL = 'http://big-event-vue-api-t.itheima.net'

const instance = axios.create({
  // TODO 1. 基础地址，超时时间
  baseURL,
  timeout: 10000
})

// 请求拦截器
instance.interceptors.request.use(
  (config) => {
    // TODO 2. 携带token(user中的token)
    const useStore = useUserStore()
    if (useStore.token) {
      config.headers.Authorization = useStore.token
    }
    return config
  },
  (err) => Promise.reject(err)
)

// 响应拦截器
instance.interceptors.response.use(
  (res) => {
    // TODO 4. 摘取核心响应数据
    if (res.data.code === 0) {
      return res
    }
    // TODO 3. 处理业务失败
    // 处理业务失败, 给错误提示，抛出错误
    ElMessage.error(res.data.message || '服务异常')
    return Promise.reject(res.data)
  },
  (err) => {
    // TODO 5. 处理401错误
    // 错误的特殊情况 => 401 权限不足 或 token 过期 => 拦截到登录
    if (err.response?.status === 401) {
      router.push('/login')
    }

    // 错误的默认情况 => 只要给提示
    ElMessage.error(err.response.data.message || '服务异常')
    return Promise.reject(err)
  }
)

export default instance
export { baseURL }

```







## 2.路由配置





- 1.组建搭建

  ![image-20230809215721507](https://raw.githubusercontent.com/binary995/img_beg/main/Picgo/image-20230809215721507.png)

![image-20230809221038335](https://raw.githubusercontent.com/binary995/img_beg/main/Picgo/image-20230809221038335.png)





- 2.配置路由

  router文件夹中的index.js

```js
import { createRouter, createWebHistory } from 'vue-router'

const router = createRouter({
  //配置history模式
  //createWebHistory 不带#号  createWebHashHistory 带#号
  history: createWebHistory(import.meta.env.BASE_URL),
  //配置路由路径
  routes: [
    //登录 login
    {
      path: '/login',
      component: () => import('@/views/login/LoginPage.vue')
    },
    //layout 一级路由
    {
      path: '/',
      component: () => import('@/views/layout/LayoutContainer.vue'),
      redirect: '/article/manage',
        //子路由
      children: [
        {
          path: '/article/manage',
          component: () => import('@/views/article/ArticleManage.vue')
        },
        {
          path: '/article/channel',
          component: () => import('@/views/article/ArticleChannel.vue')
        },
        {
          path: '/user/profile',
          component: () => import('@/views/user/UserProfile.vue')
        },
        {
          path: '/user/avatar',
          component: () => import('@/views/user/UserAvatar.vue')
        },
        {
          path: '/user/password',
          component: () => import('@/views/user/UserPassword.vue')
        }
      ]
    }
  ]
})

export default router

```

这里的`commponent`配置是直接使用`import`,

在vue2中我们使用的是先引入再使用

```js
import Home from '@/views/loginPage.vue'
{
    path:'/login',
    commponent:Home
}
```

两种方法都可







## 登录注册页面

> 功能说明
>
> 1. 注册登录   静态页面&基本切换
> 2. 注册功能 (校验+注册)
> 3. 登录 



- 完整页面

```js
<script setup>
import { userRegisterService, userLoginService } from '@/api/user.js'
import { User, Lock } from '@element-plus/icons-vue'
import { ref, watch } from 'vue'
import { useUserStore } from '@/stores'
import { useRouter } from 'vue-router'
const isRegister = ref(false)
const form = ref()

// 整个的用于提交的form数据对象
const formModel = ref({
  username: '',
  password: '',
  repassword: ''
})
// 整个表单的校验规则
// 1. 非空校验 required: true      message消息提示，  trigger触发校验的时机 blur change
// 2. 长度校验 min:xx, max: xx
// 3. 正则校验 pattern: 正则规则    \S 非空字符
// 4. 自定义校验 => 自己写逻辑校验 (校验函数)
//    validator: (rule, value, callback)
//    (1) rule  当前校验规则相关的信息
//    (2) value 所校验的表单元素目前的表单值
//    (3) callback 无论成功还是失败，都需要 callback 回调
//        - callback() 校验成功
//        - callback(new Error(错误信息)) 校验失败
const rules = {
  username: [
    { required: true, message: '请输入用户名', trigger: 'blur' },
    { min: 5, max: 10, message: '用户名必须是 5-10位 的字符', trigger: 'blur' }
  ],
  password: [
    { required: true, message: '请输入密码', trigger: 'blur' },
    {
      pattern: /^\S{6,15}$/,
      message: '密码必须是 6-15位 的非空字符',
      trigger: 'blur'
    }
  ],
  repassword: [
    { required: true, message: '请输入密码', trigger: 'blur' },
    {
      pattern: /^\S{6,15}$/,
      message: '密码必须是 6-15位 的非空字符',
      trigger: 'blur'
    },
    {
      validator: (rule, value, callback) => {
        // 判断 value 和 当前 form 中收集的 password 是否一致
        if (value !== formModel.value.password) {
          callback(new Error('两次输入密码不一致'))
        } else {
          callback() // 就算校验成功，也需要callback
        }
      },
      trigger: 'blur'
    }
  ]
}

const register = async () => {
  // 注册成功之前，先进行校验，校验成功 → 请求，校验失败 → 自动提示
  await form.value.validate()
  await userRegisterService(formModel.value)
  ElMessage.success('注册成功')
  isRegister.value = false
}

const userStore = useUserStore()
const router = useRouter()
const login = async () => {
  await form.value.validate()
  const res = await userLoginService(formModel.value)
  userStore.setToken(res.data.token)
  ElMessage.success('登录成功')
  router.push('/')
}

// 切换的时候，重置表单内容
watch(isRegister, () => {
  formModel.value = {
    username: '',
    password: '',
    repassword: ''
  }
})
</script>

<template>
  <!-- 
    1. 结构相关
      el-row表示一行，一行分成24份 
       el-col表示列  
       (1) :span="12"  代表在一行中，占12份 (50%)
       (2) :span="6"   表示在一行中，占6份  (25%)
       (3) :offset="3" 代表在一行中，左侧margin份数

       el-form 整个表单组件
       el-form-item 表单的一行 （一个表单域）
       el-input 表单元素（输入框）
    2. 校验相关
       (1) el-form => :model="ruleForm"      绑定的整个form的数据对象 { xxx, xxx, xxx }
       (2) el-form => :rules="rules"         绑定的整个rules规则对象  { xxx, xxx, xxx }
       (3) 表单元素 => v-model="ruleForm.xxx" 给表单元素，绑定form的子属性
       (4) el-form-item => prop配置生效的是哪个校验规则 (和rules中的字段要对应)
  -->
  <el-row class="login-page">
    <el-col :span="12" class="bg"></el-col>
    <el-col :span="6" :offset="3" class="form">
      <!-- 注册相关表单 -->
      <el-form
        :model="formModel"
        :rules="rules"
        ref="form"
        size="large"
        autocomplete="off"
        v-if="isRegister"
      >
      <!-- 第一行 -->
        <el-form-item>
          <h1>注册</h1>
        </el-form-item>
        <!-- 第二行 input-->
        <el-form-item prop="username">
          <el-input
            v-model="formModel.username"
            :prefix-icon="User"
            placeholder="请输入用户名"
          ></el-input>
        </el-form-item>
        <!-- 第三行 password -->
        <el-form-item prop="password">
          <el-input
            v-model="formModel.password"
            :prefix-icon="Lock"
            type="password"
            placeholder="请输入密码"
          ></el-input>
        </el-form-item>
        <!-- 第四行,请再次输入密码 -->
        <el-form-item prop="repassword">
          <el-input
            v-model="formModel.repassword"
            :prefix-icon="Lock"
            type="password"
            placeholder="请输入再次密码"
          ></el-input>
        </el-form-item>
        <!-- 第五行 注册按键 -->
        <el-form-item>
          <el-button
            @click="register"
            class="button"
            type="primary"
            auto-insert-space
          >
            注册
          </el-button>
        </el-form-item>
        <!-- 返回按键 -->
        <el-form-item class="flex">
          <el-link type="info" :underline="false" @click="isRegister = false">
            ← 返回
          </el-link>
        </el-form-item>
        
      </el-form>

      <!-- 登录相关表单 -->
      <el-form
        :model="formModel"
        :rules="rules"
        ref="form"
        size="large"
        autocomplete="off"
        v-else
      >
        <el-form-item>
          <h1>登录</h1>
        </el-form-item>
        <el-form-item prop="username">
          <el-input
            v-model="formModel.username"
            :prefix-icon="User"
            placeholder="请输入用户名"
          ></el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input
            v-model="formModel.password"
            name="password"
            :prefix-icon="Lock"
            type="password"
            placeholder="请输入密码"
          ></el-input>
        </el-form-item>
        <el-form-item class="flex">
          <div class="flex">
            <el-checkbox>记住我</el-checkbox>
            <el-link type="primary" :underline="false">忘记密码？</el-link>
          </div>
        </el-form-item>
        <el-form-item>
          <el-button
            @click="login"
            class="button"
            type="primary"
            auto-insert-space
            >登录</el-button
          >
        </el-form-item>
        <el-form-item class="flex">
          <el-link type="info" :underline="false" @click="isRegister = true">
            注册 →
          </el-link>
        </el-form-item>
      </el-form>
    </el-col>
  </el-row>
</template>

<style lang="scss" scoped>
.login-page {
  height: 100vh;
  background-color: #fff;
  .bg {
    background: url('@/assets/logo2.png') no-repeat 60% center / 240px auto,
      url('@/assets/login_bg.jpg') no-repeat center / cover;
    border-radius: 0 20px 20px 0;
  }
  .form {
    display: flex;
    flex-direction: column;
    justify-content: center;
    user-select: none;
    .title {
      margin: 0 auto;
    }
    .button {
      width: 100%;
    }
    .flex {
      width: 100%;
      display: flex;
      justify-content: space-between;
    }
  }
}
</style>

```







### 1. 静态页面完成

```js
<template>
  <!-- 
    1. 结构相关
      el-row表示一行，一行分成24份 
       el-col表示列  
       (1) :span="12"  代表在一行中，占12份 (50%)
       (2) :span="6"   表示在一行中，占6份  (25%)
       (3) :offset="3" 代表在一行中，左侧margin份数

       el-form 整个表单组件
       el-form-item 表单的一行 （一个表单域）
       el-input 表单元素（输入框）
    2. 校验相关
       (1) el-form => :model="ruleForm"      绑定的整个form的数据对象 { xxx, xxx, xxx }
       (2) el-form => :rules="rules"         绑定的整个rules规则对象  { xxx, xxx, xxx }
       (3) 表单元素 => v-model="ruleForm.xxx" 给表单元素，绑定form的子属性
       (4) el-form-item => prop配置生效的是哪个校验规则 (和rules中的字段要对应)
  -->
  <el-row class="login-page">
    <el-col :span="12" class="bg"></el-col>
    <el-col :span="6" :offset="3" class="form">
      <!-- 注册相关表单 -->
      <el-form
        :model="formModel"
        :rules="rules"
        ref="form"
        size="large"
        autocomplete="off"
        v-if="isRegister"
      >
        <el-form-item>
          <h1>注册</h1>
        </el-form-item>
        <el-form-item prop="username">
          <el-input
            v-model="formModel.username"
            :prefix-icon="User"
            placeholder="请输入用户名"
          ></el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input
            v-model="formModel.password"
            :prefix-icon="Lock"
            type="password"
            placeholder="请输入密码"
          ></el-input>
        </el-form-item>
        <el-form-item prop="repassword">
          <el-input
            v-model="formModel.repassword"
            :prefix-icon="Lock"
            type="password"
            placeholder="请输入再次密码"
          ></el-input>
        </el-form-item>
        <el-form-item>
          <el-button
            @click="register"
            class="button"
            type="primary"
            auto-insert-space
          >
            注册
          </el-button>
        </el-form-item>
        <el-form-item class="flex">
          <el-link type="info" :underline="false" @click="isRegister = false">
            ← 返回
          </el-link>
        </el-form-item>
      </el-form>

      <!-- 登录相关表单 -->
      <el-form
        :model="formModel"
        :rules="rules"
        ref="form"
        size="large"
        autocomplete="off"
        v-else
      >
        <el-form-item>
          <h1>登录</h1>
        </el-form-item>
        <el-form-item prop="username">
          <el-input
            v-model="formModel.username"
            :prefix-icon="User"
            placeholder="请输入用户名"
          ></el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input
            v-model="formModel.password"
            name="password"
            :prefix-icon="Lock"
            type="password"
            placeholder="请输入密码"
          ></el-input>
        </el-form-item>
        <el-form-item class="flex">
          <div class="flex">
            <el-checkbox>记住我</el-checkbox>
            <el-link type="primary" :underline="false">忘记密码？</el-link>
          </div>
        </el-form-item>
        <el-form-item>
          <el-button
            @click="login"
            class="button"
            type="primary"
            auto-insert-space
            >登录</el-button
          >
        </el-form-item>
        <el-form-item class="flex">
          <el-link type="info" :underline="false" @click="isRegister = true">
            注册 →
          </el-link>
        </el-form-item>
      </el-form>
    </el-col>
  </el-row>
</template>

<style lang="scss" scoped>
.login-page {
  height: 100vh;
  background-color: #fff;
  .bg {
    background: url('@/assets/logo2.png') no-repeat 60% center / 240px auto,
      url('@/assets/login_bg.jpg') no-repeat center / cover;
    border-radius: 0 20px 20px 0;
  }
  .form {
    display: flex;
    flex-direction: column;
    justify-content: center;
    user-select: none;
    .title {
      margin: 0 auto;
    }
    .button {
      width: 100%;
    }
    .flex {
      width: 100%;
      display: flex;
      justify-content: space-between;
    }
  }
}
</style>
```

### 2.注册功能的实现

> 校验+注册
>
> element 中from 表单的方法
>
> ![image-20230811201151893](https://raw.githubusercontent.com/binary995/img_beg/main/Picgo/image-20230811201151893.png)

我们使用`validata`方法来判断表单是否通过了校验

LoginPage.vue

```js
//注册事件
const form = ref()
const register = async () => {
  //进行校验,返回的是一个promise值
  await form.value.validate()
  await userRegisterService(formModel.value)
  //element 提供的alert方法
  ElMessage.success('注册成功')
  isRegister.value = false
}
```

在api中定义接口（api/user)

```js
import request from '@/utils/request.js'

export const userRegisterService = ({ username, password, repassword }) => {
  request.post('/api/reg', { username, password, repassword })
}
```

在eslintrc.cjs中补充

```js
 globals: {
    ElMessage: 'readonly',
    ElMessageBox: 'readonly',
    ElLoading: 'readonly'
  }
```

防止`ELMessage`方法报错



### 3.登录功能实习

1. 完成`login`点击事件

   ```js
   <el-link type="info" :underline="false" @click="isRegister = true">
               注册 →
   </el-link>
   
   //登录事件
   const login = async () => {
     // 进行预校验
     await form.value.validate()
     let res = await userLoginService(formModel.value)
     ElMessage.success('登录成功')
     console.log(res)
     userStore.setToken(res.data.token)
   }
   ```

2. 完成api

   api/user.js

   ```js
   // 登录事件
   export const userLoginService = ({ username, password }) => {
     return request.post('/api/login', {
       username,
       password
     })
   }
   ```

3. 登录和注册切换数据清空

   ```js
   watch(isRegister, () => {
     formModel.value = {
       username: '',
       password: '',
       repassword: ''
     }
   })
   ```

4. 将服务器返回的token进行存放

   - 将user仓库进行实例化

     ```js
     import {useUserStore} from '@/stores'
     const userStore = useUserSotre()
     ```

   - 利用user仓库中提供的setToken()

     ```js
     userStore.setToken(res.data.token)
     ```

5. 注册完成之后,路由进行跳转

   进行路由跳转

   ```js
   import {useRouter} form 'vue-router'
   
   const router =  useRouter()
   
   router.push('/')
   ```

   

   

## 首页layout架子

### 1.完成静态页面

```js
<script setup>
import {
  Management,
  Promotion,
  UserFilled,
  User,
  Crop,
  EditPen,
  SwitchButton,
  CaretBottom
} from '@element-plus/icons-vue'
import avatar from '@/assets/default.png'
</script>

<template>
  <el-container class="layout-container">
    <el-aside width="200px">
      <div class="el-aside__logo"></div>
      <!-- :default-active="$route.path" 配置默认高亮的菜单项
        router router选项开启,el-menu-item 的 index 就是点击进行跳转的对象 
      -->
      <el-menu
        active-text-color="#ffd04b"
        background-color="#232323"
        :default-active="$route.path"
        text-color="#fff"
        router
      >
        <el-menu-item index="/article/channel">
          <el-icon><Management /></el-icon>
          <span>文章分类</span>
        </el-menu-item>
        <el-menu-item index="/article/manage">
          <el-icon><Promotion /></el-icon>
          <span>文章管理</span>
        </el-menu-item>
        <el-sub-menu index="/user">
          <template #title>
            <el-icon><UserFilled /></el-icon>
            <span>个人中心</span>
          </template>
          <el-menu-item index="/user/profile">
            <el-icon><User /></el-icon>
            <span>基本资料</span>
          </el-menu-item>
          <el-menu-item index="/user/avatar">
            <el-icon><Crop /></el-icon>
            <span>更换头像</span>
          </el-menu-item>
          <el-menu-item index="/user/password">
            <el-icon><EditPen /></el-icon>
            <span>重置密码</span>
          </el-menu-item>
        </el-sub-menu>
      </el-menu>
    </el-aside>
    <el-container>
      <el-header>
        <div>黑马程序员：<strong>小帅鹏</strong></div>
        <el-dropdown placement="bottom-end">
          <span class="el-dropdown__box">
            <el-avatar :src="avatar" />
            <el-icon><CaretBottom /></el-icon>
          </span>
          <template #dropdown>
            <el-dropdown-menu>
              <el-dropdown-item command="profile" :icon="User"
                >基本资料</el-dropdown-item
              >
              <el-dropdown-item command="avatar" :icon="Crop"
                >更换头像</el-dropdown-item
              >
              <el-dropdown-item command="password" :icon="EditPen"
                >重置密码</el-dropdown-item
              >
              <el-dropdown-item command="logout" :icon="SwitchButton"
                >退出登录</el-dropdown-item
              >
            </el-dropdown-menu>
          </template>
        </el-dropdown>
      </el-header>
      <el-main>
        <router-view></router-view>
      </el-main>
      <el-footer>大事件 ©2023 Created by 黑马程序员</el-footer>
    </el-container>
  </el-container>
</template>

<style lang="scss" scoped>
.layout-container {
  height: 100vh;
  .el-aside {
    background-color: #232323;
    &__logo {
      height: 120px;
      background: url('@/assets/logo.png') no-repeat center / 120px auto;
    }
    .el-menu {
      border-right: none;
    }
  }
  .el-header {
    background-color: #fff;
    display: flex;
    align-items: center;
    justify-content: space-between;
    .el-dropdown__box {
      display: flex;
      align-items: center;
      .el-icon {
        color: #999;
        margin-left: 10px;
      }

      &:active,
      &:focus {
        outline: none;
      }
    }
  }
  .el-footer {
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 14px;
    color: #666;
  }
}
</style>



```



### 2.登录访问拦截(导航守卫)

> 在用户没有进行登录之前,只能访问登录注册页面
>
> 必须是登录过的用户才能访问



- 登录访问默认是直接放行的

- 根据返回值决定,是放行还是拦截

- 返回值:

  - 1. undefined / true 直接放行

    2. false 拦截回from的地址页面

    3. 具体路径 或 路径对象 拦截到对应的地址

       "/login"  {name:'login'}

```js
// 导航守卫
router.beforeEach((to) => {
  const userStore = useUserStore()
  if (!userStore.token && to.path !== '/login') {
    return '/login'
  }
})
```





### 3.用户基本信息获取&渲染

#### 数据获取

1. 完成获取用户信息的`userGetInfoService` 的api

   ```js
   export const userGetInfoService = () =>{
       return request.get('/my/userinfo')
   }
   ```

2. 完成store仓库

   /store/user

   ```js
   import {userGetInfoService} from '@/api/user.js'
   const user = ref({})
   const userGet = async () => {
       //发起请求
       const res = await userGetInfoService()
       user.value = res.data.data
   }
   
   return {
       user,userGet
   }
   ```

3. 在组件中获取数据

   ```js
   import {useUserStore} from '@/store'
   const userStore = useUserStore()
   
   //触发请求事件
   onMounted(()=>{
       userStore.getUser()
   })
   ```

   ![image-20230814013119403](https://raw.githubusercontent.com/binary995/img_beg/main/Picgo/image-20230814013119403.png)



#### 数据渲染

> 进行动态渲染





### 4.完善用户模块

![image-20230814182528494](https://raw.githubusercontent.com/binary995/img_beg/main/Picgo/image-20230814182528494.png)

> 完成点击用户模块时,路由的跳转和用户退出功能

路由跳转 : 进行路由跳转



用户退出:

- 清除token
- 删除user数据
- 页面退出
- 在这一切进行之前,弹窗询问用户是否再次退出



```js
 <!-- 下拉菜单 -->
        <!-- 添加监听事件  -->
        <el-dropdown placement="bottom-end"    @command="handleCommand">
          <!-- 展示给用户看的 -->
          <span class="el-dropdown__box">
            <el-avatar :src="userStore.user.user_pic || avatar" />
            <el-icon><CaretBottom /></el-icon>
          </span>
          <!-- 折叠的下拉部分 -->
          <template #dropdown>
            <el-dropdown-menu>
              <el-dropdown-item command="profile" :icon="User"
                >基本资料</el-dropdown-item
              >
              <el-dropdown-item command="avatar" :icon="Crop"
                >更换头像</el-dropdown-item
              >
              <el-dropdown-item command="password" :icon="EditPen"
                >重置密码</el-dropdown-item
              >
              <el-dropdown-item command="logout" :icon="SwitchButton"
                >退出登录</el-dropdown-item
              >
            </el-dropdown-menu>
```

> 添加监听事件之后,根据`command`的值进行判断

```js
const handleCommand = async (command) => {
  // 退出事件
  if (command === 'logout') {
      // 使用ELMessageBox 实现弹窗效果
    await ElMessageBox.confirm('是否退出', '温馨提示', {
      confirmButtonText: 'YES',
      cancelButtonText: 'NO',
      type: 'warning'
    })
    // 清空token和user数据
    userStore.removeToken()
    userStore.setUser({})
    router.push('/login')
  } else {
    //路由跳转事件
    router.push(`/user/${command}`)
  }
}
```

> 在store/user中完善对userStore的操作

``` js
//清除token
const removeToken = () = {
    token.value=''
}

//删除user数据  通过传递参数来实现清楚和添加user
const user = ref({})

const setUser = (obj) = {
    user.value = obj
}

return{
    removeToken,user,setUser
}
```







## 文章分类页面

> 基本架子  - PageContainer  封装
>
> 文章分类渲染 & loading 处理
>
> 文章分类添加编辑 
>
> 文章分类删除



### 1. 基本架子

> 在components中利用element -ui 中的card模块完成架子
>
> ![image-20230815152334221](https://raw.githubusercontent.com/binary995/img_beg/main/Picgo/image-20230815152334221.png)

```
<script setup>
    //父传子 标题
defineProps({
  title: {
    // 必填
    require: true,
    type: String
  }
})
</script>

<template>
  <el-card class="Page-container">
    <template #header>
      <div class="header">
        <span>{{ title }}</span>
        <div class="extra">
          <!-- 按钮名称 -->
          <slot name="extra"></slot>
        </div>
      </div>
    </template>
    <!-- 内容 -->
    <slot></slot>
  </el-card>
</template>

<style lang="scss" scoped>
.Page-container {
  min-height: 100%;
  box-sizing: border-box;
  .header {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
}
</style>

```

> 考虑到组件的复用,封装成组件
>
> - props 定制标题
> - 默认插槽 default 定制内容主体
> - 具名插槽 extra 定制头部左侧额外的按钮



文章分类页面`ArticleChannel`

```
<script></script>

<template>
  <page-container title="文章分类">
    <template #extra>
      <el-button type="primary">添加文章</el-button>
    </template>
    这是文章内容
  </page-container>
</template>

<style lang="scss" scoped></style>

```



### 2. 文章分类渲染 & loading 处理

- 发送api请求,从服务器获得数据

  ```js
  import request from '@/utils/request.js'
  
  export const artGetChennelService = () => {
      request.get('/my/cate/list')
  }
  ```

- 在页面发送请求

  ```js
  import {artGetChannelService} from '@/api/artical'
  
  const channelList = ref([])
  
  const getChannelList = async () =>{
      //开启loading
      loading.value = true
      const res = awaitgetChannelList()
      channelList.value = res.data.data
      //关闭loading
      loading.value = false
  }
  //触发事件
  getChannelList()
  
  //添加loading代码
  const loading = ref(false)
  ```

- 拿到数据之后,利用`element`中的`el-table`将结构代码补充出来

  ```js
  <template>
    <page-container title="文章分类">
      <template #extra>
        <el-button type="primary">添加文章</el-button>
      </template>
  //:data  将table绑定数据
  //v-loading 添加loading效果 loading的true和false 决定效果是否执行
  //el-table-column  表示一列
  //prop 绑定 channelList 里面的数据
      <el-table :data="ChannelList" style="width: 100%" v-loading="loading">
        <el-table-column type="index" prop="date" label="序号" width="100" />
        <el-table-column prop="cate_name" label="分类名称" width="180" />
        <el-table-column prop="cate_alias" label="分类别名" />
        <el-table-column label="操作" width="100">
          <!-- row 就是channelList 的一项 $index就是下标 -->
            
            //
          <template #default="scope">
            <el-button
              size="small"
              type="primary"
              plain
              :icon="Edit"
              circle
              @click="handleEdit(scope.$index, scope.row)"
            ></el-button>
            <el-button
              size="small"
              type="danger"
              plain
              :icon="Delete"
              circle
              @click="handleDelete(scope.$index, scope.row)"
            ></el-button>
          </template>
        </el-table-column>
        <!-- 添加一个空的插槽 -->
        <!-- 只要插槽为空,就显示这个 -->
        <template #empty>
          <el-empty description="description" />
        </template>
      </el-table>
    </page-container>
  </template>
  ```

  

  



### 3.文章分类添加编辑[element-plus 弹层]

> 利用element-plue中的Dlalog对话框,完成弹窗确认
>
> 对Dlalog进行封装,方便再次使用
>
> 在Dilalog中添加内容表单



#### **封装**弹层组件 ChannelEdit

编辑和添加,共用一个弹层

利用open方法,通过open()的参数,来判断 是添加还是编辑

- open方法,不带参数,就可判断为添加
- open,带参数,可判断为编辑
- 

多次使用,所以我们直接对弹窗组件进行封装`@/views/channelEdit.vue`

1. 完成Dialog组件

   ```js
   <script setup>
   import { ref } from 'vue'
   const dialogVisible = ref(false)
   
   const open = async (row) => {
     dialogVisible.value = true
     console.log(row)
   }
   
   defineExpose({
     open
   })
   </script>
   
   <template>
     <el-dialog v-model="dialogVisible" title="添加弹层" width="30%">
       <div>我是内容部分</div>
       <template #footer>
         <span class="dialog-footer">
           <el-button @click="dialogVisible = false">取消</el-button>
           <el-button type="primary"> 确认 </el-button>
         </span>
       </template>
     </el-dialog>
   </template>
   ```

   

2. 将`channelEdit`引入到`ArticleChannel`中

   通过`ref`绑定,以此拿到`channelEdit`数据

   ```js
   const dialog = ref()
   
   <channelEdit ref='dialog'><channelEdit>
   ```



3. 点击调用方法显示弹窗

   ```js
   const onAddChannel = () => {
     dialog.value.open({})
   }
   const onEditChannel = (row) => {
     dialog.value.open(row)
   }
   ```

   





4. 在channelEdit中拿到数据

   ```js
   // 通过对外暴露方法open，基于open的参数来判断是编辑还是添加
   const open = async (row) => {
     // 只要调用这份方法，就打开弹层
     dialogVisible.value = true
     console.log(row)
     //将row所有的属性复制给formModel
     formModel = { ...row } //这样添加 数据为空,编辑 => 弹层会显示当前数据
   }
   ```

   页面dialog的title根据formModel是否有id,进行判断

   ```js
    <el-dialog
       v-model="dialogVisible"
       :title="formModel.id ? '编辑分类' : '添加分类'"
       width="30%"
     >
   ```

   

