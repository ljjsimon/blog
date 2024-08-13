---
title: ionic/vue3项目最佳实践
date: 2024-08-12 19:29:00
tags:
id: 1723462264
---
# 不同环境加载不同配置

1. 安装依赖
```
npm install --save-dev dotenv
```

2. 创建环境文件
创建文件 .env.production 和 .env.development。配置名以 VITE_ 开头，可以被读到
```
VITE_API_URL=https://api.example.com
```

3. 配置 Vite
修改 vite.config.js 文件
```
import { defineConfig } from 'vite';
import vue from '@vitejs/plugin-vue';
import dotenv from 'dotenv';

export default defineConfig(({ mode }) => {
  dotenv.config({ path: `.env.${mode}` });

  return {
    plugins: [vue()],
    define: {
      'process.env': process.env
    }
  };
});
```

4. 读取配置
```
const apiUrl = import.meta.env.VITE_API_URL;
```

# 设置cdn
1. 在环境变量 .env.production .env.development 里配置 CDN URL
```
VITE_CDN_URL=https://cdn.example.com/
```

2. 在 vite.config.js 里配置 CDN URL
```
// vite.config.js
import { defineConfig } from 'vite';
import vue from '@vitejs/plugin-vue';

export default defineConfig({
  plugins: [vue()],
  base: process.env.VITE_CDN_URL,
});
```

3. 配置框架cdn
安装插件
```
npm install vite-plugin-cdn-import --save-dev
```

4. 配置 vite.config.js
```
import { defineConfig } from 'vite';
import vue from '@vitejs/plugin-vue';
import cdnImport from 'vite-plugin-cdn-import';

export default defineConfig({
  plugins: [
    vue(),
    cdnImport({
      modules: [
          {
            name: 'vue',
            var: 'Vue',
            path: 'https://cdn.jsdelivr.net/npm/vue@3.3.0/dist/vue.global.prod.js',
          },
      ],
    }),
  ],
});
```

-------------------------------------------
参考：  
[配置 Vite](http://edgeservices.bing.com/edgesvc/redirect?url=https%3A%2F%2Fjasonwatmore.com%2Fpost%2F2022%2F05%2F28%2Fvue-3-vite-access-environment-variables-from-dotenv-env&hash=qlRMfwz9ywtmxThAwOceLE54xZ6a5uqdwyp71u%2F3DQ4%3D&key=psc-underside&usparams=cvid%3A51D%7CBingProd%7CB64A68A22D9851D3A0B5B6DDB405BC9234722752C30A3D3B521126489780AE7F%5Ertone%3ABalanced)