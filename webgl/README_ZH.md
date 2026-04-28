# 目录
- [WebGL 示例](#webgl-示例)
- [KTX2 压缩、转码与显示（ktx2_encode_test）](#ktx2-压缩转码与显示ktx2_encode_test)
- [纹理视频示例（video_test）](#纹理视频示例video_test)
- [简单转码（texture_test）](#简单转码texture_test)
- [glTF 3D 模型](#gltf-3d-模型)
- [本地测试与开发](#本地测试与开发)

# WebGL 示例

需要 WebAssembly 和 WebGL 支持。WebGL 演示可在 [这里](https://binomial.biz/) 在线访问。

若要使用 [Emscripten](https://emscripten.org/) 构建编码器和转码器 WASM 库，请参见 [webgl/transcoder](https://github.com/BinomialLLC/basis_universal/tree/master/webgl/transcoder) 和 [webgl/encoder](https://github.com/BinomialLLC/basis_universal/tree/master/webgl/encoder) 目录下的 README.md 文件。C/C++ 库对应的 JavaScript API 包装器位于 [`webgl/transcoder/basis_wrappers.cpp`](https://github.com/BinomialLLC/basis_universal/blob/master/webgl/transcoder/basis_wrappers.cpp)。这个 JavaScript API 是构建在我们 C++ API 之上的一层轻量包装（但不是 C API 的包装）。

---

## KTX2 压缩、转码与显示（ktx2_encode_test）

在线演示：[`ktx2_encode_test/index.html`](https://binomial.biz/ktx2_encode_test/)

该示例展示了如何从 JavaScript 使用压缩器和转码器。使用方式是先选择一个 .PNG 文件，然后点击 “Encode!” 按钮。压缩器会在内存中动态生成一个 .ktx2 文件，随后立即对其进行转码，并使用 WebGL 像素着色器在四边形上通过 GPU 采样该纹理进行渲染。点击 “Download!” 按钮可以把生成的 .ktx2 文件下载到本地。这个示例允许用户开启或关闭本地设备支持的所有 GPU 格式，并实时查看结果。

该示例的 UI 向用户暴露了 C++ 压缩与转码 API 的相当大一部分能力。它可以在桌面浏览器和移动浏览器上运行（但需要注意，移动端上的 UI 体验并不好）。

若要查看压缩器输出的文本调试信息，请打开浏览器的开发者调试控制台（Chrome 中位于 Developer Tools），并在点击 “Encode!” 前启用 Debug 复选框。可选支持 WASM 多线程和 WASM64，推荐使用同时支持这两项能力的浏览器。

![显示 encode_test 演示的截图](ktx2_encode_test/preview.png)

---

## 纹理视频示例（video_test）

参见 [这个 wiki 页面](https://github.com/BinomialLLC/basis_universal/wiki/Encoding-ETC1S-and-XUASTC-LDR-Texture-Video)。

<img width="1185" height="610" alt="image" src="https://github.com/user-attachments/assets/1da64bbe-f74f-4650-8a35-729e2fe74f7c" />

---

## 简单转码（texture_test）

在线演示：[webgl/texture_test/index.html](https://binomial.biz/texture_test/)

渲染单张纹理，并使用转码器（通过 emscripten 编译为 WASM）生成以下压缩纹理格式之一：

* ASTC 4x4 LDR 或 HDR
* BC1（无 alpha）
* BC3、BC4 或 BC5
* ETC1（无 alpha）
* PVRTC 4bpp
* BC6H、BC7

对于不支持任何压缩纹理格式的浏览器，示例提供了一个面向不透明 LDR 纹理的低质量回退路径，以及一个面向 HDR 纹理的 HDR half float 或 LDR 32bpp 回退路径。

![在网页中以 2D 图像形式渲染 basis 纹理的截图。](texture_test/preview.png)

*注意：这个示例目前还不支持所有 ASTC/XUASTC LDR 块尺寸，只支持 4x4。请参见支持这些能力的 “ktx2_encode_test” 或 “video_test” 示例。*

## glTF 3D 模型

在线演示：[`gltf/index.html`](https://binomial.biz/gltf/)

渲染一个带有 `.basis` 纹理文件的 glTF 3D 模型，并将其转码为以下压缩纹理格式之一：

* ASTC
	* 已在 Android 上的 Chrome、Pixel 3 XL 中测试。
* DTX（BC1/BC3）
	* 已在 Chrome（Linux 和 macOS）及 Firefox（macOS）中测试。
* ETC1
	* 已在 Android 上的 Chrome、Pixel 3 XL 中测试。
* PVRTC
	* 已在 iOS iPhone 6 Plus 上的 Chrome 和 Safari 中测试。

此演示中的 glTF 模型使用了一个假设性的 `GOOGLE_texture_basis` 扩展。该扩展仅为示例而定义，glTF 格式的正式方案将通过一个新的扩展，把 Basis 文件封装在 KTX2 容器中进行嵌入；这个扩展 [目前仍在开发中](https://github.com/KhronosGroup/glTF/pull/1612)。

![显示网页中 3D 模型基础颜色纹理使用 basis 纹理渲染效果的截图。](gltf/preview.png)

## 本地测试与开发

你可以在本地托管 `webgl` 目录下的文件。一种方式是在 `webgl` 目录中使用 [Python 搭建本地 Web 服务器](https://pythonbasics.org/webserver/)：

```
cd webgl
python3 -m http.server 8000
```

**注意：若要让 WASM 多线程可用并启用（强烈推荐，以获得合理的压缩时间），服务器 [必须进行正确配置](https://unlimited3d.wordpress.com/2021/12/21/webassembly-and-multi-threading/)。请参见 `webgl/start_webserver.sh` 和 `webgl/webserver_cross_origin.py` 示例脚本。**
