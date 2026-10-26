# xradio-openwrt — Orange Pi Zero (XR819) Wi-Fi 驱动包 for OpenWrt

OpenWrt / LEDE 的外置内核模块包，驱动**香橙派 Orange Pi Zero（Allwinner H2+）板载的 XRadio XR819 SDIO Wi-Fi 芯片**。

> ⚠️ **预期管理（重要）**：XR819 芯片固件本身质量有限（大量 FCS 帧错误），吞吐和信号表现一般，仅 2.4GHz b/g/n。驱动作者原话："能用，但别用于严肃场景"。如果你追求稳定无线，**插 USB Wi-Fi 网卡仍是首选**。本包的价值在于：不插卡也能让板载 Wi-Fi 跑起来（STA / AP / 双模均支持）。

## 背景

- XR819 实为 ST-Ericsson CW1200 家族芯片的全志封装版，**从未进入 Linux 主线**；
- OpenWrt 官方拒绝打包该驱动（[openwrt#7132](https://github.com/openwrt/openwrt/issues/7132) wontfix）；
- 本包基于 [fifteenhex/xradio](https://github.com/fifteenhex/xradio) 的主线移植版（持续维护，已适配到内核 6.17+），源码钉死 commit，可复现构建；
- 固件 blob（`boot/fw/sdd_xr819.bin`）来自香橙派官方 BSP 固件（经 melsem 仓库分发，带 SHA256 校验）。

## 包内容

`xradio/` 目录是一个标准 OpenWrt package，构建后产出：

- `kmod-xradio` — 驱动内核模块 `xradio_wlan.ko`（依赖 `kmod-mac80211`，自动加载）
- 固件安装到 `/lib/firmware/xr819/`（boot_xr819.bin / fw_xr819.bin / sdd_xr819.bin）

设备树无需修改：主线内核的 `sun8i-h2-plus-orangepi-zero.dts` 已包含完整的 mmc1/pwrseq/regulator/XR819 节点。

## 使用方法

在你的 OpenWrt 源码树（LEDE/ImmortalWrt master，内核 6.x）中：

```bash
# 方式一：git clone 到 package/
git clone https://github.com/7noemo/xradio-openwrt.git
cp -a xradio-openwrt/xradio package/kernel/xr819

# 或方式二：diy 脚本中
git clone --depth 1 https://github.com/7noemo/xradio-openwrt.git xr819-repo
cp -a xr819-repo/xradio package/kernel/xr819
```

然后 `make menuconfig` 选择：

```
Kernel modules -> Wireless Drivers -> kmod-xradio
```

同时确保选了 `wpad`（或 `wpad-basic-mbedtls`）以便配置无线。

实际构建验证：[Orange-Pi-Zero-ZeroPlus-Openwrt](https://github.com/7noemo/Orange-Pi-Zero-ZeroPlus-Openwrt)（LEDE master / 内核 6.18 自动云编译，Orange Pi Zero 固件已内置本驱动）。

## 致谢

- [fifteenhex/xradio](https://github.com/fifteenhex/xradio) — 主线内核移植与长期维护
- [Icenowy](https://github.com/Icenowy/xradio) — 最初的移植工作
- [melsem](https://github.com/melsem/cw1200-xr819) — OpenWrt 打包思路与固件分发
- [香橙派官方](https://github.com/orangepi-xunlong/firmware) — XR819 固件 blob 原始来源

## License

驱动源码遵循 GPL-2.0（见上游仓库），本包打包脚本 MIT。
