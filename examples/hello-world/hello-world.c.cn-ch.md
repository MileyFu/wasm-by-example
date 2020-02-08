# Hello World!

## 概览

让我们用[C](<https://en.wikipedia.org/wiki/C_(programming_language)>)语言做一个 "Hello world" 类型的程序  using [Emscripten](https://emscripten.org)!

---

## 工具设置

首先，让我们 [获取 emsdk](https://emscripten.org/docs/getting_started/downloads.html) ，这是Emscripten获取编译器和所有工具和你需要的其它东西，要做到这一点，可以容易地使用 git 从 github 获取它:

```bash
git clone https://github.com/emscripten-core/emsdk.git
cd emsdk
```

接下来，我们可以使用 emsdk 获得最新的 Emscripten 稳定版本，并激活它，使其随时可以使用:

```bash
./emsdk install latest
./emsdk activate latest
source ./emsdk_env.sh
```

最后一个命令设置了路径，这样它就可以找到`emcc`、 Emscripten 编译器工具以及我们需要的其他所有东西。

## 编译代码

打开你最喜欢的文本编辑器，把这个 hello world 程序保存为 `hello.c`:

```c
// hello.c
#include <stdio.h>

int main() {
  printf("hello, world!\n");
  return 0;
}
```

我们可以用以下来编译

```bash
emcc hello.c -o hello.js
```

这会输出 `hello.js` 和`hello.wasm`。 Wasm 文件包含已编译的代码，而 Js 包含加载和运行它的所有代码。 默认情况下，Emscripten 输出支持在 Web 和 Node.js 中运行，所以让我们这样做：

```bash
node hello.js
```

这会像预料之中的打印出 `hello, world!` 

你也可以指示 `emcc` 来优化代码——这对代码大小非常重要——还可以为你输出 HTML，例如像这样:


```bash
emcc hello.c -O3 -o hello.html
```

然后您可以运行一个本地 web 服务器 (比如 `python -m SimpleHTTPServer 8000`) 并浏览到 `localhost:8000/hello.html`, 在这里可以看到预期的输出
