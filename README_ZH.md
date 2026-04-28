# basis_universal v2.1
一种可移植的 LDR/HDR GPU 超压缩纹理转码系统。

[![Build status](https://img.shields.io/appveyor/build/BinomialLLC/basis-universal/master.svg)](https://ci.appveyor.com/project/BinomialLLC/basis-universal)

----

简介
----

Basis Universal™ v2.1 是 Binomial LLC 开源的一个 [超压缩](http://gamma.cs.unc.edu/GST/gst.pdf) LDR/HDR GPU 压缩纹理交换系统，支持两种中间文件格式：Khronos Group 的 [.KTX2 开放标准](https://registry.khronos.org/KTX/specs/2.0/ktxspec.v2.html)，以及我们自己的 ".basis" 文件格式。这些文件格式支持快速转码到过去 25 年里几乎所有已发布的压缩 [GPU 纹理格式](https://grokipedia.com/page/texture_compression)。

## GPU 纹理是基础设施

我们的总体目标，是以兼容任意 GPU 或渲染/图形 API 的方式，简化可移植的 LDR 与 HDR GPU 纹理、图像以及短动画 [纹理视频](https://github.com/BinomialLLC/basis_universal/wiki/Encoding-ETC1S-and-XUASTC-LDR-Texture-Video) 内容的编码与高效分发。

该系统支持七种模式（或编解码器）。按实现顺序如下：
1. **ETC1S**：ETC1 的超压缩子集，专为非常快速地转码到其他 LDR 纹理格式而设计，质量低到中等但压缩率高，相比 libjpeg 转码到其他 LDR 纹理格式稍快。
2. **UASTC LDR 4x4（可带或不带 RDO）**：一种自定义、类似 ASTC 4x4 的格式，专为非常快速地转码到其他 LDR 纹理格式而设计，质量高。
3. **UASTC HDR 4x4**：标准 ASTC HDR 4x4 纹理数据，但加入了可快速转码到 BC6H 的约束。
4. **ASTC HDR 6x6（可带或不带 RDO）**：标准 ASTC HDR 6x6。
5. **UASTC HDR 6x6 Intermediate（“GPU Photo HDR”）**：超压缩 ASTC HDR 6x6。
6. **ASTC LDR 4x4-12x12（全部 14 种标准 ASTC 块尺寸，可带或不带基础窗口化 RDO）**：标准 ASTC LDR 4x4-12x12。
7. **XUASTC LDR 4x4-12x12（全部 14 种标准 ASTC 块尺寸，“GPU Photo LDR/SDR”）**：带 Weight Grid DCT（[离散余弦变换](https://grokipedia.com/page/Discrete_cosine_transform)）的潜空间超压缩 ASTC LDR，具备极高画质、极强码率伸缩性、可选自适应去块滤波（CPU 或兼容 mipmapping 与 filtering 的 [简单 GPU 像素着色器](https://github.com/BinomialLLC/basis_universal/tree/master/shader_deblocking)），支持三种熵编码配置（Zstd、算术编码或混合）。参见 [JPEG for ASTC](https://github.com/BinomialLLC/basis_universal/wiki/JPEG-for-ASTC) 以及 [ASTC and XUASTC LDR Usage Guide](https://github.com/BinomialLLC/basis_universal/wiki/ASTC-and-XUASTC-LDR-Usage-Guide)。

C/C++ 编码器和转码器库可编译为原生代码或 WebAssembly（web 或 WASI），并且所有编码/转码功能都可以通过一个 C++ 包装库从 JavaScript 访问。该包装库可选支持 [WASM 多线程](https://web.dev/articles/webassembly-threads)，以便在浏览器中实现快速编码。还支持 [WASM WASI](https://wasi.dev/) 构建，可将命令行工具和编码器/转码器作为纯 C API 的 WASI 模块使用。

现在也已提供完整的 Python 编码/转码支持，支持原生或 WASM 模块，但仍处于开发早期阶段。

许可 / 法务
-------------

本仓库中的参考编码器库、转码器以及大部分规范文档（除非另有明确说明）版权所有 © 2016–2026 Binomial LLC。除 [Apache 2.0 LICENSE](https://github.com/BinomialLLC/basis_universal/blob/master/LICENSE) 授予的权利外，保留所有权利。Basis Universal™ 是 Binomial LLC 的商标。KTX™ 是 [The Khronos Group Inc.](https://www.khronos.org/ktx/) 的商标。另见我们的 Apache 2.0 [NOTICE file](https://github.com/BinomialLLC/basis_universal/wiki/NOTICE)。如果你修改了 Basis Universal 参考源码、规范或 wiki 文档并重新分发这些文件，必须让修改后的文件带有显著声明，说明你已更改这些文件（见 Apache 2.0 第 4(b) 条）。

完整的软件及其许可证列表请参见我们的 [DEP5 file](https://github.com/BinomialLLC/basis_universal/blob/master/.reuse/dep5)。编码器库采用 Apache 2.0，但它使用了一些开源第三方模块（位于 `encoder/3rdparty` 和 `Zstd` 目录）来加载 [.QOI](https://qoiformat.org/)、[.DDS](https://github.com/DeanoC/tiny_dds)、[.EXR](https://github.com/syoyo/tinyexr) 图像，处理 [Zstd](https://github.com/facebook/zstd) 压缩，以及解包 ASTC 纹理块。参见 [LICENSES](https://github.com/BinomialLLC/basis_universal/tree/master/LICENSES) 目录。转码器除了 Zstd 之外不使用任何第三方库或依赖；Zstd 是可选的，但如果禁用，转码器将受限于不使用 Zstd 的编解码模式。

链接
-----

- [Wiki/Specifications](https://github.com/BinomialLLC/basis_universal/wiki)
- [Release Notes](https://github.com/BinomialLLC/basis_universal/wiki/Release-Notes)
- [Live Compression/Transcoding Testbed](https://binomial.biz/ktx2_encode_test/) - 推荐使用兼容 WASM64 的浏览器（如 Chrome/Edge/Firefox），尤其是在进行 XUASTC LDR 压缩时；不过普通 WASM 也能运行，只是由于可用内存更少会有分辨率限制。
- [Live WebGL Examples](https://binomial.biz/)
- [JavaScript API/WASM/WebGL info](https://github.com/BinomialLLC/basis_universal/tree/master/webgl)
- [XUASTC LDR Specification](https://github.com/BinomialLLC/basis_universal/wiki/XUASTC-LDR-Specification-v1.0)

### UASTC HDR 4x4/6x6 专用链接

- [UASTC HDR 4x4 Example Images](https://github.com/BinomialLLC/basis_universal/wiki/UASTC-HDR-Examples)
- [UASTC HDR 6x6 Example Images](https://github.com/BinomialLLC/basis_universal/wiki/ASTC-HDR-6x6-Example-Images)
- [UASTC HDR 6x6 Support Notes](https://github.com/BinomialLLC/basis_universal/wiki/UASTC-HDR-6x6-Support-Notes)
- [Quick comparison of ARM's astcenc HDR 6x6 encoder vs. ours](https://github.com/richgel999/junkdrawer/wiki/ASTC-HDR-6x6-Encoder-Comparisons)

----

支持的 LDR GPU 纹理格式
-------------------------

ETC1S、UASTC LDR 4x4、XUASTC LDR 4x4-12x12 和 ASTC LDR 4x4-12x12 文件可转码为：
- ASTC LDR 4x4 L/LA/RGB/RGBA 8bpp
- ASTC LDR 4x4-12x12（XUASTC/ASTC），0.89-8bpp
- BC1-5 RGB/RGBA/X/XY
- BC7 RGB/RGBA
- ETC1 RGB、ETC2 RGBA，以及 ETC2 EAC R11/RG11
- PVRTC1 4bpp RGB/RGBA 和 PVRTC2 RGB/RGBA
- ATC RGB/RGBA 和 FXT1 RGB
- 未压缩 LDR 栅格图像格式：8888/565/4444

支持的 HDR GPU 纹理格式
-------------------------

UASTC HDR 4x4、ASTC HDR 6x6 和 UASTC HDR 6x6 文件可转码为：
- ASTC HDR 4x4（8bpp，仅 UASTC HDR 4x4）
- ASTC HDR 6x6 RGB（3.56bpp，仅 ASTC HDR 6x6 或 UASTC HDR 6x6 中间格式）
- BC6H RGB（8bpp，UASTC HDR 4x4 或 UASTC HDR 6x6 均可）
- 未压缩 HDR 栅格图像格式：RGB_16F/RGBA_16F（half float/FP16 RGB，48 或 64bpp），或 32-bit/pixel 共享指数 [RGB_9E5](https://registry.khronos.org/OpenGL/extensions/EXT/EXT_texture_shared_exponent.txt)

----

支持的纹理压缩 / 超压缩模式
-----------------------------

1. **[ETC1S](https://github.com/BinomialLLC/basis_universal/wiki/.basis-File-Format-and-ETC1S-Texture-Video-Specification)**：一种大约 0.3-3bpp、低到中等质量的超压缩模式，基于 [ETC1](https://en.wikipedia.org/wiki/Ericsson_Texture_Compression) 的一个名为 “ETC1S” 的子集。该模式支持可变的质量/文件大小等级（类似 JPEG）、alpha 通道、内建压缩，以及可选地将纹理数组作为视频序列用跳块（[Conditional Replenishment](https://en.wikipedia.org/wiki/MPEG-1)）压缩。此模式可以快速转码为所有受支持的 LDR 纹理格式。

2. **[UASTC LDR 4x4](https://github.com/BinomialLLC/basis_universal/wiki/UASTC-LDR-4x4-Texture-Specification)**：一种 8 bits/pixel 的高质量 LDR 模式。UASTC LDR 是标准 [ASTC LDR](https://en.wikipedia.org/wiki/Adaptive_scalable_texture_compression) 4x4（8bpp）纹理格式中 19 种模式的子集，但使用了带转码提示的自定义块格式。将 UASTC LDR 转码到 ASTC LDR 和 BC7 尤其快速且简单，因为 UASTC LDR 同时是 BC7 和 ASTC 的公共子集。针对其他纹理格式的转码器则借助每个 UASTC LDR 块中若干格式专用提示位来加速。

该模式支持一个可选的 [Rate-Distortion Optimized (RDO)](https://en.wikipedia.org/wiki/Rate%E2%80%93distortion_optimization) 后处理阶段，可调整 .KTX2/.basis 文件中已编码的 UASTC LDR 纹理数据，使其更适合被 LZ 压缩。更多细节见 [这里](https://github.com/BinomialLLC/basis_universal/wiki/UASTC-implementation-details)。

这里是 [UASTC LDR 4x4 specification document](https://github.com/BinomialLLC/basis_universal/wiki/UASTC-LDR-4x4-Texture-Specification)。

3. **[UASTC HDR 4x4](https://github.com/BinomialLLC/basis_universal/wiki/UASTC-HDR-4x4-Texture-Specification)**：一种 8 bits/pixel 的高质量 HDR 模式。这是标准 [ASTC HDR](https://en.wikipedia.org/wiki/Adaptive_scalable_texture_compression) 4x4（8bpp）纹理格式中 24 种模式的子集。它被设计为高质量模式，支持 BC6H 与 ASTC 共有的 27 种分区模式，并能以极小损失（通常只有零点几 dB PSNR）快速转码到 BC6H HDR 纹理格式。特别值得注意的是，**UASTC HDR 4x4 数据是 100% 标准 ASTC 纹理数据**，因此在支持 ASTC HDR 的设备或 API 上根本不需要转码。该模式还可以转码为多种 32-64bpp 的未压缩 HDR 纹理/图像格式。

这里是 [UASTC HDR 4x4 specification document](https://github.com/BinomialLLC/basis_universal/wiki/UASTC-HDR-4x4-Texture-Specification-v1.0)，这里有一些压缩后的 [example images](https://github.com/BinomialLLC/basis_universal/wiki/UASTC-HDR-Examples)。

4. **ASTC HDR 6x6 或 RDO ASTC HDR 6x6**：一种 3.56 bits/pixel（若结合 RDO+Zstd 则更低）的高质量 HDR 模式。和模式 #3 一样，**ASTC HDR 6x6 数据是 100% 标准 ASTC 纹理数据**。相关细节见这个 [页面](https://github.com/BinomialLLC/basis_universal/wiki/UASTC-HDR-6x6-Support-Notes)。当前编码器支持 weight grid upsampling、1-3 个 subset、single 或 dual plane、CEM 7 和 11，以及所有唯一的 ASTC 分区模式。

转码模块中使用的 ASTC HDR 解码器支持完整的 ASTC HDR 格式。

5. **UASTC HDR 6x6 Intermediate（“GPU Photo HDR”）**：一种自定义压缩中间格式，可快速转码到 ASTC HDR 6x6、BC6H 以及多种未压缩 HDR 格式。该自定义压缩文件格式的说明见 [这里](https://github.com/BinomialLLC/basis_universal/wiki/UASTC-HDR-6x6-Intermediate-File-Format-(Basis-GPU-Photo-6x6))。该格式支持 75 种唯一 ASTC 配置、weight grid upsampling、1-3 个 subset、single 或 dual plane、CEM 7 和 11，以及所有唯一的 ASTC 分区模式。它还是最早支持 [delta E ITP (ICtCp) colorspace metric](https://www.portrait.com/resource-center/about-deltae-e/) 和视觉显著性图的 HDR GPU 纹理编解码器之一。

6. **标准 ASTC LDR-4x4-12x12**。支持全部 14 种标准 ASTC 块尺寸。可从任意 ASTC 块尺寸转码到任何其他受支持的 LDR 纹理格式，并支持自适应去块，包括使用 [bc7f “one-shot” analytical BC7 encoder](https://github.com/BinomialLLC/basis_universal/wiki/Transcoder-Internals-Analytical-Real-Time-Encoders)（支持全部 BC7 模式/特性）转码到 BC7，以及使用 etc1f（同样支持完整 ETC1 格式）转码到 ETC1。

转码模块中使用的 ASTC LDR 解码器支持完整标准 ASTC LDR 格式（即不仅限于我们编码器生成的 ASTC 纹理块）。ASTC LDR 转码器可以把任意块尺寸的 ASTC（4x4 - 12x12）转码到其他 LDR 纹理格式。

7. **XUASTC LDR 4x4-12x12（“GPU Photo LDR/SDR”）**：带 **Weight Grid DCT** 的超压缩 ASTC，支持全部 14 种标准 ASTC 块尺寸，在转码到其他纹理/像素格式时带有自适应去块。码率范围大约在 0.3–5.7 bpp，取决于内容、配置、块尺寸、窗口化 RDO 与 Weight Grid DCT 质量设置。典型 XUASTC LDR 4×4（**内存中为 8 bpp**）在启用 Weight Grid DCT 时的传输/磁盘码率（其效果最弱的情况下）为 **1.15–3.5 bpp（典型值约 2.25 bpp）**，而更大的块尺寸还可获得更低的可用码率，低至约 0.3 bpp。和 ASTC LDR 一样，XUASTC LDR 转码器可以将任意块尺寸 ASTC（4x4 - 12x12）转码为其他 LDR 纹理格式，但带有额外的块尺寸专项优化。

支持三种配置：基于上下文的 range/arithmetic 编码（更高压缩比）、Zstd（更快且更简单的转码），或同时使用两者的混合配置。可转码为所有其他受支持的 LDR 纹理格式，包括功能完整的 BC7（全部 8 种模式、全部双平面通道配置、全部模式设置）。某些常见块尺寸（4×4、6×6 和 8×6）对直接转码到 BC7 做了专门优化，可完全绕过分析式 BC7 编码（[bc7f](https://github.com/BinomialLLC/basis_universal/wiki/Transcoder-Internals-Analytical-Real-Time-Encoders)），直接针对最常见的 ASTC 配置（纯色与单 subset CEM）进行处理。

Weight Grid DCT 可以被禁用；不过仍可配合可选、可配置的窗口化 RDO 实现超压缩。兼容所有主要图像和纹理内容类型，包括摄影图像、光照贴图、反照率/高光纹理、各种法线贴图、仅亮度贴图以及地理空间映射信号。

支持在从较大块尺寸转码时进行自适应去块；可通过转码器标志关闭。

XUASTC LDR 的一个很有意思的用途，且适用于全部 14 种块尺寸：以相较旧系统极低的码率高效分发纹理内容，从而显著缩短下载时间。结合更大的 XUASTC 块尺寸（超过 6x6）、Weight Grid DCT，以及在 CPU 或 [GPU 简单着色器](https://github.com/BinomialLLC/basis_universal/tree/master/shader_deblocking) 上进行的自适应去块，**任何开发者现在都能以 0.35-1.5 bpp 分发最终目标为 BC7 的纹理和图像内容**，并且**可将转码后的 BC7 数据缓存到现代 Gen 4 或 5（10+ GB/sec.）SSD 上**。

XUASTC LDR 支持如下 ASTC 配置：L/LA/RGB/RGBA CEM；base+scale 或 RGB/RGBA direct；base+offset CEM；Blue Contraction 编码；1–3 个 subsets；全部分区模式；以及 single 或 dual-plane 模式。这里是 [XUASTC LDR specification](https://github.com/BinomialLLC/basis_universal/wiki/XUASTC-LDR-Specification-v1.0)。另见 [ASTC and XUASTC LDR Usage Guide](https://github.com/BinomialLLC/basis_universal/wiki/ASTC-and-XUASTC-LDR-Usage-Guide)。

下面这张图展示了 XUASTC LDR 4x4（算术配置 vs. Zstd 配置）在 151 张测试纹理/图像上的码率与失真关系（与我们用于创建 [bc7e.ispc](https://github.com/richgel999/bc7enc_rdo) 的测试集相同）。失真采用 [PSNR-HVS-M](https://pypi.org/project/psnr-hvsm/) 衡量。另一个在 effort 9、不同块尺寸下生成的率失真图可见我们的 wiki [这里](https://github.com/BinomialLLC/basis_universal/wiki/JPEG-for-ASTC#rate-vs-distortion-graph-at-various-block-sizes)。
<img width="1284" height="760" alt="image" src="https://github.com/user-attachments/assets/60f56279-3efd-4ec9-b866-a456e6fa9735" />

说明：
- 模式 #1（ETC1S）对基础时域超压缩（[texture video](https://github.com/BinomialLLC/basis_universal/wiki/Encoding-ETC1S-and-XUASTC-LDR-Texture-Video)）提供特殊支持和优化。
- 模式 #3（UASTC HDR 4x4）、#4（RDO ASTC HDR 6x6）和 #6（ASTC LDR 4x4-12x12）输出的是 100% 标准 ASTC 纹理数据（无论是否使用 RDO），和其他 ASTC 编码器没有区别。.KTX2 文件就是普通纹理。
- 其他模式（#1、#2、#5、#7）输出的是多种自定义超压缩格式中的压缩数据，我们的转码库可以实时将它们转换为多种 GPU 纹理或像素格式。
- 模式 #4（ASTC HDR 6x6）和 #5（UASTC HDR 6x6）内部使用相同的统一 ASTC HDR 6x6 编码器。
- 模式 #6（ASTC LDR 4x4-12x12）和 #7（XUASTC LDR 4x4-12x12）内部使用相同的统一 ASTC LDR ASTC 编码器。

### 其他特性

所有模式下，.basis 和 .KTX2 文件都支持 mipmap 级别、纹理数组、立方体贴图、立方体贴图数组，以及纹理视频。此外，.basis 文件支持非均匀纹理数组，也就是文件中的每张图像都可以拥有不同的分辨率或 mipmap 数量。

在 ETC1S 模式下，压缩器可以利用整个文件中所有图像之间的颜色和模式相关性，使用全局 endpoint/selector codebook，因此能够高效地在单个文件中存储带 mipmap 的多张图像。ETC1S 模式还支持对短视频序列使用跳块（Conditional Replenishment），以避免发送相对于前一帧未变化的块。

支持读取的 LDR 图像格式包括 .PNG、[.DDS with mipmaps](https://learn.microsoft.com/en-us/windows/win32/direct3ddds/dx-graphics-dds-pguide)、.TGA、.QOI 和 .JPG。支持读取的 HDR 图像格式包括 .EXR、.HDR 以及带 mipmap 的 .DDS。该库可以写出 .basis、.KTX2、.DDS、.KTX（v1）、.ASTC、.OUT、.EXR 和 .PNG 文件。

系统现在支持加载基础 2D .DDS 文件（可选 mipmaps），但 .DDS 文件必须使用受支持的未压缩格式之一：24bpp RGB、32bpp RGBA/BGRA、half-float RGBA 或 float RGBA。使用 .DDS 文件可以让用户精确控制压缩前 mipmap 的生成方式。

----

构建（原生）
-------------

编码库和命令行工具不依赖任何仓库中未包含的必需第三方依赖。转码器是单个 .cpp 源文件（位于 `transcoder/basisu_transcoder.cpp`），没有第三方依赖。

我们在以下环境下进行构建和测试：
- Windows x86/x64，使用 Visual Studio 2026、MSVC 或 clang
- Windows ARM，使用 Visual Studio 2022 ARM 17.13.0
- Ubuntu Linux 24.04.3 LTS（noble），使用 gcc 13.3.0 或 clang 18.1.3
- macOS（M1），使用 clang 16.0.0
- Arch Linux ARM，运行在 [Pinebook Pro](https://pine64.org/devices/pinebook_pro/) 上，使用 gcc 12.1
- Ubuntu Linux 24.04 on RISC-V（Orange PI RV2）
- cmake: 3.28.3，emcc: 4.0.19

在 Windows + Visual Studio 下，可以直接使用仓库自带的 `basisu.sln`。或者你也可以用 cmake 生成新的 VS 解决方案 / 项目文件。

构建前，先 [安装 cmake](https://cmake.org/)，然后执行：

```
mkdir build
cd build
cmake ..
make
```

要在 x86/x64 系统上启用 SSE 4.1 支持进行构建（ETC1S 编码大约快 15-30%），请在 cmake 命令行中加入 `-DBASISU_SSE=TRUE`。加入 `-DBASISU_OPENCL=TRUE` 可启用（可选）OpenCL 支持。使用 `-DCMAKE_BUILD_TYPE=Debug` 构建调试版本。若要构建 32 位可执行文件，加入 `-DBASISU_BUILD_X64=FALSE`。

构建完成后，用于创建、校验以及转码 / 解包 .KTX2/.basis 文件的原生命令行工具为 `bin/basisu`。

*注意：我们使用 C++17 编译该软件。更高版本对我们来说还太新，暂未正式支持用更新的 C++ 标准编译。*

----

运行预编译的 WASM WASI 可执行文件
-----------------------------------

对于较小的图像/纹理（约 4 megapixels 或更小），`bin` 目录中已提交了预编译、安全、跨平台的 32 位 .WASM WASI 可执行文件：`basisu_mt.wasm`（多线程）和 `basisu_st.wasm`（单线程）。下面是使用 [wasmtime](https://wasmtime.dev/) 对 ETC1S/UASTC LDR 4x4（所有平台）进行快速测试的示例，多线程和单线程各一条：

使用 wasmtime v39.0.0 测试：

```
cd bin
wasmtime run --dir=. --dir=../test_files --wasm threads=yes --wasi threads=yes ./basisu_mt.wasm -test
wasmtime run --dir=. --dir=../test_files ./basisu_st.wasm -test
```

对于更新版本的 wasmtime，例如 v42.0.1，需要额外加上 `--wasm shared-memory=yes`：

```
wasmtime run --dir=. --dir=../test_files --wasm threads=yes --wasm shared-memory=yes --wasi threads=yes ./basisu_mt.wasm -test
```

可参见 `runwt.sh`、`runwt.bat`、`runw.sh` 或 `runw.bat`，了解如何使用 wasmtime 运行这些 WASM 可执行文件。下面是一个 Windows 下使用算术配置、Weight Grid DCT 级别 70 进行 XUASTC LDR 6x6 压缩的例子：

```
cd bin
runwt.bat ../test_files/tough.png -xuastc_ldr_6x6 -quality 70 -xuastc_arith
runwt.bat tough.ktx2
```

Linux/macOS：

```
cd bin
chmod +x runwt.sh
./runwt.sh ../test_files/tough.png -xuastc_ldr_6x6 -quality 70 -xuastc_arith
./runwt.sh tough.ktx2
```

不过，32 位 WASM WASI 可执行文件相比原生可执行文件存在一些权衡：内存受限、性能较慢（可通过我们支持的 WASM 线程在一定程度上缓解）。**32 位 WASM WASI 的内存限制会把 ASTC LDR 或 XUASTC LDR 可压缩的最大图像/纹理尺寸限制在约 4 megapixels。**（其他编解码器的内存需求更低。）在 Web 上，我们同时支持 WASM 和 WASM64（可带或不带线程），这极大改善了 WASM 的内存问题。据我们截至 2026 年 2 月所知，wasmtime 已支持 WASM64，但 WASI SDK 仍 [未正式支持 wasm64-wasi target](https://github.com/WebAssembly/wasi-sdk/issues/212)；一旦支持，我们也会跟进支持。

构建（WASM WASI）
------------------

要构建 WASM WASI 可执行文件，需要先安装 [WASM WASI SDK](https://github.com/WebAssembly/wasi-sdk)。同时必须将 `WASI_SDK_PATH` 环境变量设置为 SDK 的实际安装路径。

多线程：
```
mkdir build_wasm_mt
cd build_wasm_mt
cmake -DCMAKE_TOOLCHAIN_FILE=$WASI_SDK_PATH/share/cmake/wasi-sdk-pthread.cmake -DCMAKE_BUILD_TYPE=Release -DBASISU_WASM_THREADING=ON ..
make
```

单线程：
```
mkdir build_wasm_st
cd build_wasm_st
cmake -DCMAKE_TOOLCHAIN_FILE=$WASI_SDK_PATH/share/cmake/wasi-sdk.cmake -DCMAKE_BUILD_TYPE=Release -DBASISU_WASM_THREADING=OFF ..
make
```

构建得到的 WASM WASI 可执行文件会被放到 `bin` 目录。这些跨平台可执行文件功能完整，可使用 [wasmtime](https://github.com/bytecodealliance/wasmtime) 之类的 WASM WASI 运行时执行。

----

### 测试编解码器

命令行工具内置了一些自动化的 LDR/HDR 编码/转码测试：

```
cd ../bin
basisu -test
basisu -test_hdr_4x4
basisu -test_hdr_6x6
basisu -test_hdr_6x6i
basisu -test_xuastc_ldr
```

要在 OpenCL 模式下测试编解码器（必须安装 OpenCL 库 / 头文件 / 驱动，并在运行 cmake 时通过 `-DBASISU_OPENCL=TRUE` 编译进 OpenCL 支持）：

```
basisu -test -opencl
```

----

压缩与解包 .KTX2/.basis 文件
------------------------------

- 将一张 LDR sRGB PNG/QOI/TGA/JPEG/DDS 图像压缩为超压缩的 XUASTC LDR 6x6 .KTX2 文件，质量级别 75（**合法质量范围 1-100，值越高质量越高**），effort 级别 4（**合法 effort 范围 0-10，值越高压缩越慢，默认 effort 为 3**）：

`basisu -xuastc_ldr_6x6 -quality 75 -effort 4 x.png`

`-quality 100` 会禁用 Weight Grid DCT，仅保留 ASTC 的无损超压缩。`-xuastc_ldr_6x6` 的别名是 `-ldr_6x6i`（其中 `i` 表示 “intermediate”）。支持全部 **[14 种标准 ASTC 块尺寸](https://developer.nvidia.com/astc-texture-compression-for-game-assets)**，范围从 4x4 到 12x12：4x4、5x4、5x5、6x5、6x6、8x5、8x6、10x5、10x6、8x8、10x8、10x10、12x10 和 12x12。**XUASTC LDR 到 BC7 的转码器对几个常见块尺寸做了特殊优化：4x4、6x6 和 8x6。** 当在这些特定块尺寸上转码 XUASTC LDR 时，大多数 XUASTC 块会被*直接*转码为 BC7（即直接从 XUASTC 潜变量空间到 BC7 潜变量空间），跳过实时分析式 bc7f 编码步骤。

更多 XUASTC LDR 专用选项（其中许多也适用于标准 ASTC，见 [ASTC/XUASTC Usage Guide](https://github.com/BinomialLLC/basis_universal/wiki/ASTC-and-XUASTC-LDR-Usage-Guide)）：

	- `-xuastc_arith`、`-xuastc_zstd`（默认）和 `-xuastc_hybrid` 控制所使用的 **XUASTC LDR 配置**。算术配置以转码吞吐为代价，相比 Zstd 配置获得大约 5-18% 更好的压缩率；混合配置则介于两者之间。

	- `-ts` 或 `-srgb` 启用 **sRGB 配置（默认）**，`-tl` 或 `-linear` **启用线性配置**。理想情况下该设置应与 GPU 采样 ASTC 纹理的方式一致。法线贴图请使用 linear。

	- `-weights X Y Z W` 设置无符号整型 **通道误差权重**，用于在压缩时优先照顾某些通道。

	- 另一组 XUASTC 专用选项可覆盖 **窗口化 RDO 行为**（窗口化或有界 RDO 是相对于 Weight Grid DCT 的另一套独立且可选的感知优化）：`-xy` 启用窗口化 RDO，`-xyd` 禁用窗口化 RDO。默认情况下，如果未启用 Weight Grid DCT（即未指定 `-quality`，或其值为 100），则窗口化 RDO 关闭。如果质量小于 100，则会自动启用窗口化 RDO，除非指定了 `-xyd`。另请参见工具的 [help text](https://github.com/BinomialLLC/basis_universal/blob/master/cmd_help/cmd_help.txt)，获取更多窗口化 RDO 选项，例如 `-ls_min_psnr`、`-ls_min_alpha_psnr`、`-ls_thresh_psnr`、`-ls_thresh_alpha_psnr` 等。

	- `-xs` 禁用 2-3 subset，`-xp` 禁用 dual plane（压缩率略高，且会更频繁地触发更快的直接 BC7 转码）
	- `-higher_quality_transcoding`：允许更慢但质量更高的转码
	- `-no_deblocking`：在 ASTC 块尺寸 > 8x6 时禁用自适应去块（更快）
	- `-force_deblocking`：即便块尺寸 <= 8x6，也始终使用自适应去块滤波（更慢）
	- `-stronger_deblocking`：在启用时使用更强的去块（性能相同）
	- `-fast_xuastc_ldr_bc7_transcoding` 和 `-no_fast_xuastc_ldr_bc7_transcoding`：控制更快的直接 XUASTC->BC7 转码（默认启用，质量会略低一些）

- 将一张 LDR sRGB 图像压缩为标准 ASTC LDR 6x6 .KTX2 文件，使用 effort 级别 4（合法范围 0-10）：

`basisu -astc_ldr_6x6 -effort 4 x.png`

`-astc_ldr_6x6` 的别名是 `-ldr_6x6`。

和 XUASTC LDR 一样，支持全部 14 种标准 ASTC 块尺寸，从 4x4 到 12x12。内部依然使用 XUASTC LDR 编码器，但输出的是标准 ASTC 块数据，而不是超压缩的 XUASTC LDR。大多数 XUASTC LDR 选项在 ASTC LDR 模式下同样可用。

- 将一张 LDR sRGB 图像压缩为 ETC1S .KTX2 文件，质量级别 100（最高）：

`basisu -quality 100 x.png`

- 对于线性 LDR 图像，在 ETC1S 模式下使用默认质量（`-quality 50`，或旧参数 `-q 128`）：

`basisu -linear x.png`

- 压缩为 UASTC LDR 4x4。它的质量远高于 ETC1S，但最高质量低于 ASTC/XUASTC LDR 4x4：

`basisu -uastc x.png`

- 将 [.EXR](https://en.wikipedia.org/wiki/OpenEXR)、[Radiance .HDR](https://paulbourke.net/dataformats/pic/) 或 .DDS HDR 图像压缩为 UASTC HDR 4x4 .KTX2 文件：

`basisu x.exr`

- 压缩为标准 ASTC HDR 6x6 文件（约 3.56 bpp）：

```
basisu -hdr_6x6 x.exr
basisu -hdr_6x6 -lambda 500 x.exr
basisu -hdr_6x6_level 5 -lambda 500 x.exr
```

- 压缩为 UASTC HDR 6x6i 文件（使用压缩中间格式，以换取更小文件，约 1.75-3.0 bpp）：

```
basisu -hdr_6x6i x.exr
basisu -hdr_6x6i -lambda 500 x.exr
basisu -hdr_6x6i_level 5 -lambda 500 x.exr
```

请注意，统一的 `-quality` 和 `-effort` 选项在 HDR 模式中同样适用。以上示例使用的是旧版、非统一选项，它们能提供更直接、更精细的控制。

还要注意，我们使用的 .EXR 读取器来自 [TinyEXR](https://github.com/syoyo/tinyexr)，它并不支持所有可能的 .EXR 压缩模式。可使用 [ImageMagick](https://imagemagick.org/) 之类的工具生成 TinyEXR 可读取的 .EXR 文件。

另外，也可以通过指定 `-hdr`、`-hdr_6x6` 或 `-hdr_6x6i`，把 LDR 图像（例如 .PNG）压缩到 HDR 格式。默认情况下，LDR 图像在压缩为 HDR 格式时，会先从 sRGB 转换为线性光，并缩放到 100 [nits - candela per sq. meter, cd/m²](https://grokipedia.com/page/Candela_per_square_metre)。可通过 `-hdr_ldr_no_srgb_to_linear` 禁用 sRGB 转换步骤，并通过 `-hdr_ldr_upconversion_nit_multiplier X` 修改从归一化 RGB 线性光到 nit 的放大系数。

注意：如果你把 LDR/SDR 图像文件压缩到 HDR 格式，编解码器的默认行为是先将 8 位图像数据转换为线性光（即反转 sRGB 传递函数），然后再把线性光 RGB 值乘以 LDR->HDR 上变换系数，该系数单位为 nit。在旧版本中，这个系数实际上约等于 1 nit，但现在所有模式默认都为 100 nit。（典型 LDR 显示器亮度约为 80-100 nit。）若要修改它，请使用命令行选项 `-hdr_ldr_upconversion_nit_multiplier X`。（这样做的原因是 HDR 6x6 编解码器内部使用 [ICtCp HDR colorspace](https://en.wikipedia.org/wiki/ICtCp)。LDR/SDR 图像必须先上变换为线性光 HDR 图像，并根据图像在实际 SDR/HDR 显示器上的显示方式，缩放到合适的最大亮度。）

### 一些实用命令行选项

- 所有编解码器现在都支持统一的 `quality` 与 `effort` 设置。`-effort X` [0,10] 控制压缩搜索空间的大小（以及压缩速度会变多慢），`-quality X` [1,100] 控制质量与码率之间的权衡。内部会把这些统一选项映射到各编解码器的具体配置设置。几乎所有旧选项仍然可用。此前 `-q X`（X 范围 [1,255]）用于控制 ETC1S 质量；该选项仍保留，但现在更推荐使用 `-quality`。

- `-debug` 会让编码器打印内部和面向开发者的详细调试信息。

- `-stats` 用于查看各种质量（PSNR）统计信息。

- `-linear`：ETC1S 默认使用 sRGB 色彩空间度量，UASTC LDR 当前始终使用线性度量，而 UASTC HDR 默认使用加权 RGB 度量（权重为 2,3,1）。如果输入是法线贴图，或其他非 sRGB（非摄影类）纹理内容，请务必使用 `-linear`，以避免额外且没有必要的伪影。（UASTC LDR/HDR 的角度法线贴图度量是完全可行的，也在我们的 TODO 列表中。）

- 指定 `-opencl` 会启用 OpenCL 模式，目前它只会在编译时已启用 OpenCL 支持的情况下，加速 ETC1S 编码。

- 压缩器默认启用多线程，可通过 `-no_multithreading` 关闭。当前转码器仍为单线程，但它是线程安全的（也就是说支持并行解压多个纹理切片）。

更多命令行示例
----------------

- 将一张 sRGB PNG/QOI/TGA/JPEG/DDS 图像压缩为带 mipmap 的 RDO（Rate-Distortion Optimization）UASTC LDR .KTX2 文件：

`basisu -uastc -uastc_rdo_l 1.0 -mipmap x.png`

`-uastc_rdo_l X` 控制 RDO（[Rate-Distortion Optimization](https://en.wikipedia.org/wiki/Rate%E2%80%93distortion_optimization)）质量设置。该值越低，质量越高，但压缩文件也越大。建议尝试的值在 0.2-3.0 之间。默认值为 1.0。

- 为 ETC1S .KTX2 文件添加自动生成的 mipmaps：

`basisu -mipmap -quality 75 x.png`

有多种 mipmap 相关选项可以调整滤波核、RGB 通道所用滤波色彩空间（linear vs. sRGB）、最小 mipmap 尺寸等。工具还支持生成 cubemap 文件、2D/cubemap 纹理数组等。若要绕过自动 mipmap 生成器，可以先生成 LDR 或 HDR 的未压缩 [.DDS texture files](https://learn.microsoft.com/en-us/windows/win32/direct3ddds/dx-graphics-dds-pguide)，再将其交给压缩器。

- 在默认质量级别（128）下创建一个画质略高的 ETC1S .KTX2 文件（也就是 endpoint/selector codebook 质量更高的文件） - 注意这样编码会慢很多：

`basisu -comp_level 2 x.png`

对于一些少见图像（例如蓝天渐变），你可能需要提高 ETC1S 的 `-comp_level` 设置，取值范围 1 到 6。它控制编码器为优化 ETC1S 码本和压缩数据流所投入的整体 effort。更高的 `-comp_level` 会*显著*降低编码速度。

- 手动设置 ETC1S 码本大小（而不是使用 `-quality` 或旧版 `-q` 选项），并使用更高的码本生成级别（这在 texture video 中很有用）：

`basisu x.png -comp_level 2 -max_endpoints 16128 -max_selectors 16128`

- 使用 Reinhard tonemap 算子，将 HDR .EXR 或 .HDR 图像文件 [tonemap](https://en.wikipedia.org/wiki/Tone_mapping) 成多张不同曝光的 LDR .PNG 文件：

`basisu -tonemap x.exr`

- 比较两张 LDR 图像并打印 PSNR 统计：

`basisu -compare a.png b.png`

- 比较两张 HDR .EXR/.HDR 图像并打印 FP16 PSNR 统计：

`basisu -compare_hdr a.exr b.exr`

完整的命令行选项列表请参见帮助文本。命令行工具本质上只是编码器库之上的一个薄包装。

将 .KTX2/.basis 文件解包为 .PNG/.EXR/.KTX/.DDS 文件
------------------------------------------------------

你既可以使用命令行工具，也可以从 JavaScript 或 C/C++ 代码中 [直接调用转码器](https://github.com/BinomialLLC/basis_universal/wiki/How-to-Use-and-Configure-the-Transcoder)，把 .KTX2/.basis 文件解压为 GPU 纹理数据或未压缩图像数据。要把 .KTX2 或 .basis 文件解包为多份 .png/.exr/.ktx/.dds 文件：

`basisu x.ktx2`

使用 `-no_ktx` 和 `-etc1_only`/`-format_only` 选项，可以减少输出文件数量。

`-info` 和 `-validate` 只会显示文件信息，不会输出任何文件。

写出的带 mipmap、cubemap 或纹理数组的 .KTX/.DDS 文件会使用多种压缩 GPU 纹理格式（PVRTC1 4bpp、ETC1-2、BC1-5、BC7 等）。据我们所知，不幸的是（截至 2024 年）仍然没有一个单一的 .KTX 或 .DDS 查看工具，能够正确且稳定地支持我们所支持的全部 GPU 纹理格式。BC1-5 和 BC7 可用 AMD Compressonator 查看，ETC1/2 可用 Mali Texture Compression Tool 查看，PVRTC1 可用 Imagination Tech 的 PVRTexTool 查看。[RenderDoc](https://renderdoc.org/) 也提供了一个对许多格式都很有用的纹理查看器。macOS 的 *Finder* 应用可预览多种 GPU 格式下的 .EXR、.ASTC 和 .KTX 文件，包括 ASTC LDR/HDR。Windows 11 资源管理器可预览 .DDS 文件。[online OpenHDR Viewer](https://viewer.openhdr.org/) 适合查看 .EXR/.HDR 图像文件。

----

像素着色器去块示例：CPU + GPU 全面去块
------------------------------------------

仓库中的 [shader_deblocking sample](https://github.com/BinomialLLC/basis_universal/blob/master/shader_deblocking/README.md) 展示了如何使用一个简单的像素着色器，对任意 4x4-12x12 块尺寸的采样纹理进行去块，大幅减少块状伪影。示例着色器兼容 mipmapping 和双线性 / 三线性过滤。归根结底，着色器去块让更大的 ASTC 块尺寸变得可用，从而降低码率并提高转码速度。去块是现代图像与视频编解码器的标准特性，没有理由不能在采样（或转码）GPU 纹理时使用。使用更大的 ASTC 块尺寸还能显著降低 GPU 内存带宽消耗。如果带宽是瓶颈，而它通常确实是，那么去块所增加的适度 ALU 与纹理采样开销，实际上可能几乎等于免费。

XUASTC LDR 的转码器在转码到 BC7 这类其他（非 ASTC）格式时支持自适应去块，而对 ASTC 则可以使用 GPU 着色器去块，因此形成了一套完整的 ASTC 去块体系。

----

Python 支持
-----------

现在，所有关键编码功能和全部转码功能都可从 Python 调用，但仍处于开发早期阶段。如何构建原生 SO/PYD，请参见 python 目录中的 README 文件。Python 支持模块同时支持原生和 WASM 模块，当原生库无法加载时会回退到 WASM。当前已在 Ubuntu Linux 和 Windows 11 上测试过 Python 支持。

示例：
```
cd python
python3 -m tests.test_backend_loading
========== BACKEND LOADING TEST ==========

Testing native backend...
[Encoder] Using native backend
	[OK] Native backend loaded
Hello from basisu_wasm_api.cpp version 200
	Native get_version() ? 200
	Native alloc() returned ptr = 190977024
	Native free() OK
	[OK] Native basic operations working.

Testing WASM backend...
[WASM Encoder] Loaded: /mnt/c/dev/xuastc4/python/basisu_py/wasm/basisu_module_st.wasm
[Encoder] Using WASM backend
	[OK] WASM backend loaded
Hello from basisu_wasm_api.cpp version 200
	WASM get_version() ? 200
	WASM alloc() returned ptr = 26920160
	WASM free() OK
	[OK] WASM basic operations working.

========== DONE ==========
```

----

WebGL 示例
----------

`WebGL` 目录包含若干简单的 WebGL 演示，它们使用了通过 [Emscripten](https://emscripten.org/) 编译为 [WASM](https://webassembly.org/) 的转码器和压缩器。这些演示在线地址在 [这里](https://binomial.biz/)。更多细节见 [这里](webgl/README.md) 的 readme 文件。

![Screenshot of 'texture' example running in a browser.](webgl/texture_test/preview.png)
![Screenshot of 'gltf' example running in a browser.](webgl/gltf/preview.png)
![Screenshot of 'encode_test' example running in a browser.](webgl/ktx2_encode_test/preview.png)

----

使用 [Emscripten](https://emscripten.org/) 构建 WASM 模块
---------------------------------------------------------

转码器和编码器都可以使用 Emscripten 编译为 WebAssembly，并用于 Web。位于 [`webgl/transcoding/basis_wrappers.cpp`](https://github.com/BinomialLLC/basis_universal/blob/master/webgl/transcoder/basis_wrappers.cpp) 的一组 JavaScript 包装器，以带有 Emscripten 扩展的 C++ 编写。该 JavaScript 包装器支持几乎全部特性和模式，包括 texture video。参见 `webgl/transcoder` 和 `webgl/encoder` 中的 [README.md](https://github.com/BinomialLLC/basis_universal/tree/master/webgl) 与 CMakeLists.txt。

构建 WASM 转码器，在安装好 Emscripten 之后执行：

```
cd webgl/transcoder/build
emcmake cmake ..
make
```

构建 WASM 编码器：

```
cd webgl/encoder/build
emcmake cmake ..
make
```

还有几个简单的编码 / 转码 Web 演示，位于 `webgl/ktx2_encode_test` 和 `webgl/texture_test`，展示了如何使用编码器和转码器的 JavaScript 包装 API。在线演示见 [这里](https://binomial.biz/)。

----

底层 C++ 编码器 / 转码器 API 示例
----------------------------------

一些直接调用 C++ 编码器与转码器库 API 的简单示例位于 [`example/example.cpp`](https://github.com/BinomialLLC/basis_universal/blob/master/example/example.cpp)。

----

使用 vcpkg 依赖管理器安装
---------------------------

你可以使用 [vcpkg](https://github.com/Microsoft/vcpkg/) 依赖管理器下载并安装 Basis Universal：

		git clone https://github.com/Microsoft/vcpkg.git
		cd vcpkg
		./bootstrap-vcpkg.sh
		./vcpkg integrate install
		vcpkg install basisu

vcpkg 中的 Basis Universal port 由 Microsoft 团队成员和社区贡献者持续维护。如果版本过旧，请在 vcpkg 仓库中 [提交 issue 或 pull request](https://github.com/Microsoft/vcpkg)。（2024/9/10：这里暂时还不提供 UASTC HDR 支持。）

---

项目策略
--------

参见我们的 wiki 页面：[Project Policies: PRs, compiler warnings, release cadence etc.](https://github.com/BinomialLLC/basis_universal/wiki/Project-Policies:-PR's,-compiler-warnings,-release-cadence,-etc.)。

----

KTX2 支持状态
--------------

请注意，截至 2026 年 3 月，我们仍在与 Khronos 协作，完善如何将 XUASTC LDR 超压缩纹理数据嵌入 KTX2 文件格式的具体细节。使用我们此前编解码器的 KTX2 纹理文件（包括最近新增的 UASTC HDR 4x4 和 UASTC HDR 6x6i 格式）现在已经可以与其他 KTX2 工具互通。更多信息请参见我们的 [KTX2 technical information document](https://github.com/BinomialLLC/basis_universal/wiki/KTX2-File-Format-Support-Technical-Details)。

只要有可能，我们都会保持对以往写出的所有 KTX2 文件的完整自省 / 转码兼容性，即便在标准化过程中发生了文件格式变更。我们预计在 basisu v2.1 中 XUASTC LDR 嵌入 KTX2 的方式不会再变。

----

使用 REUSE 的仓库许可证管理
------------------------------

该仓库现已更新，符合 REUSE 许可证检查工具（https://reuse.software/）的要求。参见 [.reuse](https://github.com/BinomialLLC/basis_universal/tree/master/.reuse) 子目录。

外部链接
--------

- [btx - KTX2 Command Line Tool](https://github.com/BinomialLLC/KTX-Software-Binomial-Fork) - 我们对 KTX-Software 的分支，包含 bug 修复、可用的 HDR quality/effort 控制、新选项和新编解码器集成。该工具可以校验、提取并压缩与本项目兼容的 KTX2 文件。
- [ARM's astcenc](https://github.com/ARM-software/astc-encoder) - ARM 官方的重要工具，可将 ASTC 格式的 LDR/HDR .astc 和 .ktx 文件解包为 .png 或 .exr，以便测试和验证。
- [Online .EXR and .HDR Image File Viewer](https://viewer.openhdr.org/) - OpenHDR Viewer。其 tone mapper、自动曝光和 HDR 直方图实现都非常完善。
- [RenderDoc](https://renderdoc.org/) - 可靠的 LDR/HDR .DDS 查看器，支持 BC1-7 格式。打开 .PNG/.EXR/.DDS 文件后，选择 Window->Texture Viewer。支持 mipmaps、纹理数组、cubemaps 和基础曝光控制。
- [Paint.NET](https://www.getpaint.net/) - Windows 应用：内置 [.DDS file loading](https://github.com/0xC0000054/pdn-ddsfiletype-plus)，支持 BC1-7 和 [cubemaps](https://github.com/0xC0000054/pdn-ddsfiletype-plus/wiki/Cube-Maps)
- [dds-ktx](https://github.com/septag/dds-ktx)：简单的 .DDS/.KTX 查看器，支持 mipmaps。（注意：对于任一维度上不能被 4 texel 整除的压缩纹理会崩溃，这属于 [Direct3D specific limitation](https://learn.microsoft.com/en-us/windows/win32/direct3d10/d3d10-graphics-programming-guide-resources-block-compression)。）
- [AMD Compressonator](https://gpuopen.com/compressonator/) - .DDS 查看器，也可以查看某些格式的 .KTX 文件。
- [PVRTexTool](https://www.imgtec.com/developers/powervr-sdk-tools/pvrtextool/) - 可查看某些 AMD Compressonator 无法显示的 .ASTC 和 .KTX 文件格式。（注意：其 .DDS 查看器对 BC1 支持似乎有问题，而且完全不支持 BC7。）
- [Microsoft's DirectXTex](https://github.com/microsoft/DirectXTex) - 示例中包含一个基础 .DDS 查看器。（注意：对纹理尺寸不能被 4 texel 整除的 .DDS 文件，可能仍存在加载问题。）
- [Windows HDR + WCG Image Viewer](https://13thsymphony.github.io/hdrimageviewer/) - 面向 Windows 的真正 HDR 图像查看器，可在 HDR 显示器上工作。另见 [the github repo](https://github.com/13thsymphony/HDRImageViewer)。
- [Mali Texture Compression Tool](https://community.arm.com/support-forums/f/graphics-gaming-and-vr-forum/52390/announcement-mali-texture-compression-tool-end-of-life) - 已废弃。
- [Our GitHub wiki content statically mirrored as HTML](https://subquantumtech.com/basisu_wiki/Home.html)，其内容会落后于 [live GitHub wiki](https://github.com/BinomialLLC/basis_universal/wiki)

更多实用链接、论文和工具 / 库，请参见 [UASTC HDR 4x4 texture specification](https://github.com/BinomialLLC/basis_universal/wiki/UASTC-HDR-4x4-Texture-Specification#papersfurther-reading) 末尾部分。

----

安全说明
--------

集成到本仓库中的 Basis Universal 编解码器，其安全报告与修复由 Binomial 负责。报告方式请参见上方 GitHub Security 标签页，或文件 [SECURITY.md](https://github.com/BinomialLLC/basis_universal/blob/master/SECURITY.md)。

----

电子邮件：info @ binomial dot info，或通过 [Twitter](https://twitter.com/_binomial) 联系我们。

这里是 [Sponsors](https://github.com/BinomialLLC/basis_universal/wiki/Sponsors-and-Supporters) wiki 页面。
