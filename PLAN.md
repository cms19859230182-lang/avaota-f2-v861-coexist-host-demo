# PLAN — 档 B（个人板仓）短计划

> 时间：2026-11-04 Asia/Shanghai · 这只是顺序表，不代表已完成。

## 本包状态（已完成的部分，HOST-only）

- [x] B-HOST-1 打包共存 boot stub：`bins/c907_boot_coexist_efex.bin` + SHA（8560 B）
- [x] B-HOST-2 打包 E907 hello：`bins/e907_hello_coexist.bin` + SHA（182 B）
- [x] B-HOST-3 stock 只读参考副本 3 枚（注明来源 `../stock-fullcopy/bins/`）
- [x] B-HOST-4 diff：`docs/diff-coexist-vs-stock.patch`
- [x] B-HOST-5 文档：`README.md`／`NOTICE`／`LAYOUT.md`／`PLAN.md`／本 DONE

## 待办（全部需要 FEL，本刀不做）

### B-F1 — FEL 冷链验证（不改 stock 顺序，仅换 boot）
1. `init_dram_efex.bin @0x00100000` write+exec
2. `smp_test_fel.bin @0x40000000` write
3. **换** `c907_boot_coexist_efex.bin @0x00100000` write+exec
4. 判据（全部满足才算过）：
   - UART 出现 `starting the C907`、SyterKit v0.5.0 banner、`SMP: hart 1 is up`、五项自测 PASS；
   - **不**再出现 light-sleep／WFI 卡死迹象。

### B-F2 — E907 存活 + 共享槽观测
1. 加载 `e907_hello_coexist.bin @0x40100000`（entry），随 B-F1 同一上板会话；
2. 观测 `0x47FFF000`：magic == `0xE907C001`，seq 单调递增；
3. 达成后**才**允许写 `E907-COEXIST-ALIVE` marker（本包内**不存在**该 marker）。

## 之后（不在档 B 范围）
- 档 C：RT-Thread 板级 BSP（finsh + 点灯为门槛；MSGBOX 后置）。

## 禁区
- 禁 `git push`／gh／上传／PR。
- 禁改已有 bin、禁改 SyterKit 上游源（本包只读引用）。
- 禁回头刀2q（`ALIVE=0` 已封存）、禁把 V853 msgbox 地址抄到 V861。
- 禁编造 UART 输出；未上板就写「未做 FEL」。
