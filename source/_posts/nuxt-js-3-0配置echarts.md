---
title: nuxt.js 3.0配置echarts
date: 2023-06-26 20:35:12
tags: nuxt.js
---

# 背景

nuxt 3.0直接引入vue-echarts会报错，按以下配置修改即可。

# 配置
```
// nuxt.config.js
// https://nuxt.com/docs/api/configuration/nuxt-config
export default defineNuxtConfig({
  devtools: { enabled: true },
  modules: [
    '@element-plus/nuxt'
  ],
  elementPlus: {},
  buildModules: ["@nuxtjs/composition-api/module"],
  build: {
    transpile: [/echarts/, /zrender/, /tslib/, /resize-detector/]
  },
})

```

```
// pages/index.vue
<template>
  <div>
    <client-only>
      <v-chart class="chart" :option="option" />
    </client-only>
  </div>
</template>

<script>
import { use } from 'echarts/core';
import { CanvasRenderer } from 'echarts/renderers';
import { PieChart } from 'echarts/charts';
import {
  TitleComponent,
  TooltipComponent,
  LegendComponent,
} from 'echarts/components';
import VChart, { THEME_KEY } from 'vue-echarts';
import { ref, defineComponent } from 'vue';

use([
  CanvasRenderer,
  PieChart,
  TitleComponent,
  TooltipComponent,
  LegendComponent,
]);

export default defineComponent({
  name: 'HelloWorld',
  components: {
    VChart,
  },
  provide: {
    [THEME_KEY]: 'dark',
  },
  setup() {
    const option = ref({
      title: {
        text: 'Traffic Sources',
        left: 'center',
      },
      tooltip: {
        trigger: 'item',
        formatter: '{a} <br/>{b} : {c} ({d}%)',
      },
      legend: {
        orient: 'vertical',
        left: 'left',
        data: ['Direct', 'Email', 'Ad Networks', 'Video Ads', 'Search Engines'],
      },
      series: [
        {
          name: 'Traffic Sources',
          type: 'pie',
          radius: '55%',
          center: ['50%', '60%'],
          data: [
            { value: 335, name: 'Direct' },
            { value: 310, name: 'Email' },
            { value: 234, name: 'Ad Networks' },
            { value: 135, name: 'Video Ads' },
            { value: 1548, name: 'Search Engines' },
          ],
          emphasis: {
            itemStyle: {
              shadowBlur: 10,
              shadowOffsetX: 0,
              shadowColor: 'rgba(0, 0, 0, 0.5)',
            },
          },
        },
      ],
    });

    return { option };
  },
});
</script>

<style scoped>
.chart {
  height: 100vh;
}
</style>

```
# 报错记录

## 错误1 tslib
运行 **yarn build** 后，会报以下错：
```
[nuxt] [request error] [unhandled] [500] Cannot find module 'C:\Users\jianqiao\Desktop\remoteControl\.output\server\node_modules\tslib\modules\index.js' imported from C:\Users\jianqiao\Desktop\remoteControl\.output\server\chunks\app\server.mjs
```
### 解决办法
把 **/tslib/** 添加到 **transpile** 中。

> 注：通过alias添加tslib，还是会有问题，见参考文章nuxt github issue

## 错误2 ESM错误

运行 **yarn build** 后，会报以下错：
```
[Vue Router warn]: uncaught error during route navigation:
file:///C:/Users/jianqiao/Desktop/remoteControl/.output/server/chunks/app/_nuxt/index-318c28a5.mjs:2
import { addListener, removeListener } from 'resize-detector';
         ^^^^^^^^^^^
SyntaxError: Named export 'addListener' not found. The requested module 'resize-detector' is a CommonJS module, which may not support all module.exports as named exports.
CommonJS modules can always be imported via the default export, for example using:

import pkg from 'resize-detector';
const { addListener, removeListener } = pkg;

    at ModuleJob._instantiate (node:internal/modules/esm/module_job:127:21)
    at async ModuleJob.run (node:internal/modules/esm/module_job:191:5)
    at async Promise.all (index 0)
    at async ESMLoader.import (node:internal/modules/esm/loader:337:24)
    at async setup (file:///C:/Users/jianqiao/Desktop/remoteControl/.output/server/chunks/app/server.mjs:470:76)     
    at async Object.callAsync (file:///C:/Users/jianqiao/Desktop/remoteControl/.output/server/node_modules/unctx/dist/index.mjs:72:16)
    at async applyPlugin (file:///C:/Users/jianqiao/Desktop/remoteControl/.output/server/chunks/app/server.mjs:124:35)
    at async applyPlugins (file:///C:/Users/jianqiao/Desktop/remoteControl/.output/server/chunks/app/server.mjs:140:7)
    at async createNuxtAppServer (file:///C:/Users/jianqiao/Desktop/remoteControl/.output/server/chunks/app/server.mjs:957:7)
    at async Object.renderToString (file:///C:/Users/jianqiao/Desktop/remoteControl/.output/server/node_modules/vue-bundle-renderer/dist/runtime.mjs:176:19)
```

### 解决办法

参考最后的nuxt官方文档中的esm，把 **/resize-detector/** 添加到 **transpile** 中


# 参考文章
[nuxt github issue](https://github.com/nuxt/bridge/issues/25)

[github nuxt-bridge-echarts repo](https://github.com/AndreyYolkin/nuxt-bridge-echarts)

[nuxt doc ems](https://nuxt.com/docs/guide/concepts/esm)