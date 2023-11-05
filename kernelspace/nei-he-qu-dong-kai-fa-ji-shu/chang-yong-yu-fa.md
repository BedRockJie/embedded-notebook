---
description: 内核中常用语法操作屏蔽关键信息
---

# ✈ 常用语法

## 编译器忽略运行检查

声明函数时在前面加上 `__maybe_unused` 可以跳过编译器检查。

```c
static int __maybe_unused axienet_dma_probe(struct platform_device *pdev,
					    struct net_device *ndev)
```
