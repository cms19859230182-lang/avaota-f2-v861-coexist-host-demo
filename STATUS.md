MARKER=DEMO-PACK-B-OK

# 档 B 演示仓 · 共存 HOST 产物打包

- 时间：2026-11-07 Asia/Shanghai
- 范围：**HOST-only** 打包（无 FEL、无上板）
- 结论：本仓已公开，两枚 coexist bin 已入库且 **SHA256 与已知一致**。

## 1. 路径清单

```
.
├── README.md                                    中文说明
├── NOTICE                                       GPL-2.0 + libdram 闭源 stub 声明
├── LAYOUT.md                                    SRAM boot / DRAM hello / 共享槽 / 禁踩区
├── PLAN.md                                      B-F1 / B-F2 待 FEL
├── STATUS.md                                    本文件
├── SHA256SUMS.txt                               本包全部 bin 的 SHA256
├── bins/
│   ├── c907_boot_coexist_efex.bin               8560 B  HOST 派生（共存 boot）
│   ├── e907_hello_coexist.bin                    182 B  HOST 派生（E907 hello）
│   ├── SHA256SUMS.coexist-host.txt              原样复制自 coexist-host 产物
│   ├── SHA256SUMS.e907-hello-coexist.txt        原样复制自 e907-hello-coexist 产物
│   ├── init_dram_efex.bin                       29568 B stock 只读副本
│   ├── c907_boot_efex.bin                        8624 B stock 只读副本
│   └── smp_test_fel.bin                          7936 B stock 只读副本
└── docs/
    └── diff-coexist-vs-stock.patch              与 stock 的差异补丁
```

## 2. SHA256 校验（实测，sha256sum）

| 文件 | 字节 | SHA256 | 期望 | 结果 |
|---|---|---|---|---|
| `bins/c907_boot_coexist_efex.bin` | 8560 | `b7442be6fbf485c0b2bf36ed411d9f5ea38088880f6296f15eaa4053b638d404` | `b7442be6…b638d404` | **OK** |
| `bins/e907_hello_coexist.bin` | 182 | `42e426a0f9f53b95a8cc7635d85884f5009f2efd338b7045320fedffde75c559` | `42e426a0…75c559` | **OK** |

stock 参考副本（未改动）：

| 文件 | SHA256 |
|---|---|
| `bins/init_dram_efex.bin` | `0493812f91497d5627caa846a4742fc4b8940b893ecedad1c731434ac7e76eaf` |
| `bins/c907_boot_efex.bin` | `538aed5c920a0be328cef2da56b2085286c5de815939be54ea07b9774daea529` |
| `bins/smp_test_fel.bin` | `3b702c1174a9acee477af0beb47a1bd91bd39c24174c43afc320bf527a36ec1a` |

## 3. 引用的上游 HOST 证据（未改动，只读引用）

- coexist-host 产物 — MARKER=COEXIST-HOST-OK；槽 `0x47FFF000` magic `0xE907C001`
- e907-hello-coexist 产物 — MARKER=E907-HELLO-HOST-OK；entry `0x40100000`
- stock 全量副本 — `STOCK-UART-OK`（原冷链三 bin）

## 4. 明确未做（必须如实）

- **未做 FEL**；**未上板**。
- **无真板共存 marker**：包内**不存在** `E907-COEXIST-ALIVE`，待 FEL（见 PLAN.md B-F1／B-F2）。
- 未改 SyterKit 源、未改任何已有 bin、未编造 UART 输出。
- 不是 RT-Thread 上游 PR／BSP 完成；与档 A stock 仓并列。

## 5. 后续

B-F1：FEL 冷链只换 boot → 要 `starting the C907` + SyterKit/SMP 仍 PASS。
B-F2：加载 hello @`0x40100000`，观测 `0x47FFF000` magic `0xE907C001` + seq 递增 → 才可写 ALIVE marker。