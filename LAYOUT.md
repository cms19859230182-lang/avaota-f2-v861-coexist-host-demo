# LAYOUT — 共存三区布局（SRAM boot / DRAM hello / 共享槽）

> 依据 `../coexist-host/`、`../e907-hello-coexist/` 的 HOST 构建产物与源码布局。
> 数值为编译期常量；**未经 FEL 真板验证**。

## 1. SRAM boot 区（ARM / A 核 boot stub）

| 项 | 值 |
|---|---|
| 加载地址 | `0x00100000`（SRAM，与 stock `c907_boot_efex.bin` 同址） |
| 产物 | `bins/c907_boot_coexist_efex.bin`（8560 B） |
| FEL 动作 | write + exec |
| 与 stock 差异 | 删 `mexstatus` light-sleep 与 `for(;;) wfi`（见 `docs/diff-coexist-vs-stock.patch`） |

stock 冷链中此位置先后承载 `init_dram_efex.bin`、`c907_boot_efex.bin`；
共存链只替换 boot 这一跳，`init_dram` / `smp_test` 保持原样。

## 2. DRAM hello 区（RISC-V / E907 侧最小 hello）

| 项 | 值 |
|---|---|
| 入口地址 | `0x40100000` |
| 产物 | `bins/e907_hello_coexist.bin`（182 B） |
| 布局 | 与 boot stub 切开，独立链接（`linker-coexist.ld`） |
| 行为 | 起来后向共享槽写 magic／seq，然后不抢 UART |

## 3. 共享槽（SRAM 页，跨核可见）

| 项 | 值 |
|---|---|
| 基址 | `0x47FFF000` |
| magic | `0xE907C001` |
| 内容 | magic（u32） + seq（u32），boot 与 hello 两侧共同约定 |
| 读法 | A 核轮询（不抢 UART）；真板待 FEL 观测 |

## 4. 禁踩区

| 地址 | 原因 |
|---|---|
| `0x40000000` | `smp_test_fel.bin` 落点（SMP 保留区），共存产物**不得**写入或越界覆盖 |
| `0x504` 类 MMIO | 盲拧寄存器已封存（刀2q `ALIVE=0`），**勿回头** |
| UART0 | 共存链**不抢写**；hello 只走共享槽 |

## 5. 内存图（示意）

```
0x00100000  [ SRAM ]  c907_boot_coexist_efex.bin   (write+exec)
0x40000000  [ DRAM ]  smp_test_fel.bin             (禁踩)
0x40100000  [ DRAM ]  e907_hello_coexist.bin       (entry)
0x47FFF000  [ SRAM ]  共享槽 magic=0xE907C001 + seq
```

> 地址冲突／重叠问题若在 FEL 阶段暴露，改 HOST 侧链接脚本，**不要**改 stock bin。
