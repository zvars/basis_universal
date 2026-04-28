`basisu_wrappers.cpp` 包含我们的 JavaScript API（通过 [emscripten](https://emscripten.org/) 绑定实现），它是在编码器和转码器 C++ API 之上的一层轻量封装。从 Basis Universal v2.0 开始，它支持可选的 WASM 多线程和 WASM64。

`build/` 目录中已包含预构建版本的 `basis_transcoder.js` 和 `basis_transcoder.wasm`，足够用于本地演示。若要自行构建转码器，请先安装 emscripten（[教程](https://webassembly.org/getting-started/developers-guide/)）和 cmake（[下载](https://cmake.org/download/)）。然后运行：

```shell
cd webgl/transcoder/build/
emcmake cmake ../
make
```
