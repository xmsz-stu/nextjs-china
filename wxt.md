# wxt



#### 如何避免每次dev时chrome清空所有登录信息

{% embed url="https://wxt.dev/guide/essentials/config/browser-startup.html#persist-data" %}



#### chrome自动断开？

{% embed url="https://github.com/wxt-dev/wxt/issues/460#issuecomment-3041673360" %}

{% embed url="https://github.com/wxt-dev/wxt/issues/1023" %}





*   除了 `storage.sync` 以外，默认情况下，WXT 扩展在普通模式和隐身模式之间不会有任何其他的共享。

    这意味着：

    1. **没有直接的内存共享：** 普通模式和隐身模式的 background script, popup, options 页面等 entrypoint 运行在不同的进程或上下文中，它们各自拥有独立的内存空间。在其中一个模式下修改变量，不会影响到另一个模式下的变量。
    2. **没有共享的文件系统：** 即使扩展程序可以访问文件系统（例如，通过读取或写入文件），普通模式和隐身模式下访问的文件也是隔离的。
    3. **没有共享的 DOM：** Content script 在页面中注入 JavaScript 代码，但普通模式和隐身模式下的 content script 注入的是不同的页面实例，因此它们之间没有 DOM 共享。

    **总结:**

    `storage.sync` 是在普通模式和隐身模式之间共享数据的唯一官方方式。 如果你需要更复杂的数据共享机制，可能需要考虑使用外部服务器或其他高级技术，但这通常是不必要的。





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
