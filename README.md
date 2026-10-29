# AI 通话静音

去掉**小爱 / AI 助理自动接听**的电话在挂断时的那一声「嘟」和那一下震动。

HyperOS / MIUI 上，「对方挂断」时 Telecom 会在**通话流**上放一声提示音并震一下，静音模式和勿扰都拦不住。
小爱帮你接的电话里对方总是先挂，所以这个声音几乎必然出现。本模块只对「AI 接听」的通话生效，自己接的电话行为不变。

- **源码 / 详细说明**：[clansty/com.clansty.telecomaimute](https://github.com/clansty/com.clansty.telecomaimute)
- **要求**：KernelSU / Magisk + Zygisk + LSPosed 2.x（libxposed API 101/102）
- **作用域**：系统框架（已静态声明，装完只需启用）

## 安装

1. 下载 release 里的 apk 安装（命令行安装请用 `adb install --no-incremental`，否则 LSPosed 读不到模块 APK）
2. 在 LSPosed 里启用「AI 通话静音」
3. 重启

## 原理

- 声音：`CallAudioManager.playToneForDisconnectedCall()` → `InCallTonePlayer(toneId=2)` → `ToneGenerator(TONE_PROP_PROMPT)`，走 VOICE_CALL 流
- 震动：`MiuiRinger.onCallStateChanged()` → `vibrateWhenConnectOrDisconnect()` → `HapticCompat.performExtHapticFeedback(206)`
- 两者都只在「对方挂断」（`DisconnectCause.Code == REMOTE`）时触发；用 `acceptRingingCallInternal` 的调用方包名（`com.miui.voiceassist`）判断是否为 AI 接听

## License

MIT
