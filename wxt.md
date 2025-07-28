# wxt



#### 如何避免每次dev时chrome清空所有登录信息

{% embed url="https://wxt.dev/guide/essentials/config/browser-startup.html#persist-data" %}



#### chrome自动断开？

{% embed url="https://github.com/wxt-dev/wxt/issues/460#issuecomment-3041673360" %}

{% embed url="https://github.com/wxt-dev/wxt/issues/1023" %}



#### 设置启动页

```
import { defineConfig } from "wxt";

// See https://wxt.dev/api/config.html
export default defineConfig({
  webExt: {
    startUrls: ["https://wxt.dev"],
  },
});
```
