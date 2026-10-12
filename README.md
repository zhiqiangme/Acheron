# 黄泉 · 终结技实时渲染

崩坏：星穹铁道「黄泉」终结技的网页实时渲染：Unity 2022.3 WebGL（URP + [StarRailNPRShader](https://github.com/stalomeow/StarRailNPRShader)）。
画面逐帧对应同目录工程的 Goo Engine 离线成片（645 帧，60 fps，10.75 秒）。可暂停、逐帧查看、切换自由视角。

在线地址：<https://acheron.varka.cn/>（GitHub Pages 托管，自定义域名由仓库根目录 `CNAME` 文件绑定，DNS 在 DNSPod 以 CNAME 指向 `zhiqiangme.github.io`）。

## 本地打开

WebAssembly 不能用 `file://` 直接打开，需要一个本地 HTTP 服务。在本目录执行：

```bash
python -m http.server 8000
```

然后在浏览器打开 <http://localhost:8000>。

构建时启用了 gzip 和解压回退（decompression fallback），因此不配置 `Content-Encoding` 的静态服务器也能直接托管，包括 GitHub Pages、`python -m http.server` 和 Nginx 默认配置。

## 操作

| 操作 | 按键 / 控件 |
| --- | --- |
| 播放 / 暂停 | 空格（自动模式），或播放按钮 |
| 自动 / 手动 | 「自动 / 手动」，默认自动连播。手动模式在开场、斩一、斩二末尾慢放停顿，按空格、「下一段」或等待 3 秒进入下一段；斩三与收尾连播，播完即停 |
| 逐帧 | ← / →（按住 Shift 一次 10 帧） |
| 跳到片段 | 「开场 / 三斩 / 收尾」 |
| 速度 | 0.25× / 0.5× / 1× |
| 自由视角 | F，或「自由视角」。左键环绕、右键平移、滚轮缩放；触屏单指环绕、双指缩放 |

## 还原范围

| 内容 | 来源 |
| --- | --- |
| 开场（1–231 帧）、收尾（365–645 帧）镜头 | 游戏 `Avatar_Acheron_00_Ani_Skill03_Start/End_Camera` 曲线逐帧换算。与原 `.anim` 对比，位置误差 < 1e-6 m |
| 三斩（232–364 帧）镜头 | 按游戏战斗镜头配置 `Avatar_Acheron_00_Skill31/32/33_Camera` 求解。锚点和敌阵位置为标定值 |
| 角色动作 | 游戏 `Skill03_Start / 01 / 02 / 03 / End` 动画。蓝形态（avatar_00）用于开场，红形态（avatar_01）用于三斩与收尾 |
| 黑白保红调色 | 游戏 LUT `Acheron_Ultra_LUT`（LogC 输入、线性输出），第 190 帧起生效 |
| 命中、径向模糊、抖动、色差的时刻 | 技能配置 `WaitAnimState` 与 End Timeline 后处理轨道 |
| 刀光、命中花、碎片、白环、天空、水面 | 对照实机录屏近似重做，不是游戏特效资源 |

不包含敌人模型，命中特效落在标定的敌阵位置。卡通着色使用 StarRailNPRShader 的默认参数，未逐项导入游戏材质数值。

## 目录

```text
index.html        页面与播放控制（Unity WebGL 模板生成）
Build/            Unity WebGL 构建产物
TemplateData/     样式、封面、图标
```

## 重新构建

源工程不在本仓库：

- Unity 工程：`D:\Project\Blender\Model\Star Rail\Acheron_Ultimate_Unity`。批处理入口为 `Acheron.EditorTools.AcheronSetup.SetupAll` / `BuildWebGL`，见 `Assets/Acheron/Editor/AcheronSetup.cs`；WebGL 模板在 `Assets/WebGLTemplates/Acheron/`。
- 镜头、特效与后处理时间线：由 `D:\Project\Blender\Tools\Scripts\acheron_skill03_unity\bake_goo_skill03_for_unity.py` 从三段 Goo 特效工程逐帧烘焙。

## 许可与声明

- 非官方同人作品，仅供学习交流。角色模型、动作、贴图等游戏资源版权归 miHoYo / HoYoverse 所有。
- 构建产物包含 [StarRailNPRShader](https://github.com/stalomeow/StarRailNPRShader)（GPL-3.0）编译后的着色器与脚本，其源代码见该仓库（本构建使用 fork [zhiqiangme/StarRailNPRShader](https://github.com/zhiqiangme/StarRailNPRShader) `webgl` 分支的 `5be450a`：在 `bf44567` 基础上去掉角色着色器按渲染目标索引的 Blend/ColorMask，否则 WebGL2 会判这些 Pass 不支持、角色不显示）。
