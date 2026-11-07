# 档 B · 个人板仓 — Avaota F2（V861/V881）共存 HOST 产物打包

一句话：把**共存 HOST 侧**已跑通的产物（boot stub + E907 hello）打成本地证据包。
**不是上游 PR**，**不是** RT-Thread BSP 完成，**没有**真板 E907 存活证据。

## 边界（读前必看）

- 纯 **HOST-only** 打包：只汇总已有 bin、SHA、diff、说明。
- **未做 FEL**：本包内**没有任何** FEL 上板动作、**无**真板 `E907-COEXIST-ALIVE` marker。
- 真板共存 marker **未采**，**待 FEL**（见 `PLAN.md` B-F1／B-F2）。
- 禁 `git push`／gh／上传；本包只落在本地仓。

## V861 vs V881（同一颗硅）

板名 **V861** / Avaota F2；`xfel version` 实测 `AWUSBFEX ID=0x00191800(V881)`。
SyterKit 内部按 V881 适配（`chips/v881.c`）；「V861 板 / V881 ID」不是插错板。

## 地址表（禁踩区）

| 用途 | 地址 | 说明 |
|---|---|---|
| E907 hello **entry** | `0x40100000` | hello 入口，DRAM |
| 共享槽 **magic/seq** | `0x47FFF000` | magic `0xE907C001`，SRAM 槽页 |
| **SMP 保留** | `0x40000000` | `smp_test_fel.bin` 落点，**禁踩** |
| boot stub 加载 | `0x00100000` | `c907_boot_coexist_efex.bin`，SRAM boot 区 |

详见 `LAYOUT.md`。

## 包内 bin 与 SHA256

| 文件 | 字节 | SHA256 |
|---|---|---|
| `bins/c907_boot_coexist_efex.bin` | 8560 | `b7442be6fbf485c0b2bf36ed411d9f5ea38088880f6296f15eaa4053b638d404` |
| `bins/e907_hello_coexist.bin` | 182 | `42e426a0f9f53b95a8cc7635d85884f5009f2efd338b7045320fedffde75c559` |

stock 只读参考副本（来源 `../stock-fullcopy/bins/`，未改动）：
`init_dram_efex.bin`、`c907_boot_efex.bin`、`smp_test_fel.bin`。

共存与 stock 的差异见 `docs/diff-coexist-vs-stock.patch`（仅尾部改动）。

## 下游来源与下一刀

- `../coexist-host/` MARKER=COEXIST-HOST-OK；`../e907-hello-coexist/` MARKER=E907-HELLO-HOST-OK；`../stock-fullcopy/` STOCK-UART-OK。
- 下一刀 FEL：原冷链只换 boot → 要 `starting the C907` + SyterKit/SMP 仍 PASS，**且** E907 侧共享槽心跳可见（`0x47FFF000` magic `0xE907C001`）。

## 致谢

SyterKit 作者 **BernardXiong**（YuzukiHD）与上游维护者。许可见 `NOTICE`。
