# Hello World!

**在开始之前，请检查所有可用的语言，点击标题中的“语言”下拉菜单。**

## 概览

对于我们的第一个程序，我们将在 [AssemblyScript](https://github.com/AssemblyScript/assemblyscript)中执行“ Hello world”类型的程序。 AssemblyScript本质上是一个从TypeScript到 WebAssembly 编译器。 这对于想要编写 WebAssembly的TypeScript 和 JavaScript 开发人员来说非常棒，他们不需要学习新的语言。 但是，需要明确的是，你没有办法使用AssemblyScript将TypeScript React apps编译到WebAssembly，因为它的 TypeScript不同。但是它仍然是构建 WebAssembly 应用程序的一种很好的语言。

为方便说明，使用[介绍示例](/example-redirect?exampleName=introduction&programmingLanguage=all)中提到的 Wasm 的限制,我们将两个数字加到一起并展示结果。然而我们要记住，在后面的示例当中，许多这些限制将被您选择的 WebAssembly 语言(在本例中为 AssemblyScript)抽象出来。

---

## Implementation执行

那么首先让我们创建我们的 `hello-world.ts` AssemblyScript文件:

```typescript
// 这导出一个add函数
// 它接受两个32位整数值
// 并返回一个32位的整数值。
导出函数add(a: i32, b: i32): i32 {
  返回 a + b;
}
```

然后，让我们使用 [AssemblyScript 编译器](https://docs.assemblyscript.org/details/compiler)将它编译为wasm模块，然后会输出一个 `hello-world.wasm`:

```bash
asc hello-world.ts -b hello-world.wasm
```

然后，让我们创建一个 `hello-world.js` JavaScript文件，并加一个加载 Wasm模块的函数：

```javascript
// https://github.com/torch2424/wasm-by-example/blob/master/demo-util/
导出 const wasmBrowserInstantiate = async (wasmModuleUrl, importObject) => {
  let response = undefined;

  if (!importObject) {
    importObject = {
      env: {
        abort: () => console.log("Abort!")
      }
    };
  }

  // 检查浏览器是否支持流的实例化
  if (WebAssembly.instantiateStreaming) {
    // fetch该模块，并在其下载的时候将其实例化
    response = await WebAssembly.instantiateStreaming(
      fetch(wasmModuleUrl),
      importObject
    );
  } else {
    // 回退到使用 fetch 下载整个模块
    // 然后实例化该模块
    const fetchAndInstantiateTask = async () => {
      const wasmArrayBuffer = await fetch(wasmModuleUrl).then(response =>
        response.arrayBuffer()
      );
      return WebAssembly.instantiate(wasmArrayBuffer, importObject);
    };
    response = await fetchAndInstantiateTask();
  }

  return response;
};
```

接下来，让我们使用上面来自`hello-world.js`的 Javascript 函数， 装载并实例化我们的 wasm 模块 `hello-world.wasm`。 然后，继续`hello-world.js` 我们将从 Wasm 模块中调用导出的 `add()` 函数:

```javascript
const runWasmAdd = async () => {
  // 实例化我们的 wasm 模块
  const wasmModule = await wasmBrowserInstantiate("./hello-world.wasm");

  //  从 wasm 调用 add 函数 export，保存结果
  const addResult = wasmModule.instance.exports.add(24, 24);

  // 将结果set 到body上
  document.body.textContent = `Hello World! addResult: ${addResult}`;
};
runWasmAdd();
```

最后让我们加载 ES6 模块, `hello-world.js` Javascript file in our `index.html`:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Hello World - AssemblyScript</title>
    <script type="module" src="./hello-world.js"></script>
  </head>
  <body></body>
</html>
```

我们这有了一个可以运行的 Wasm Add (Hello World)程序! 恭喜！


您就会得到类似下面 demo ([Source Code](/source-redirect?path=examples/hello-world/demo/assemblyscript)) 的东西:

## Demo

<iframe title="AssemblyScript Demo" src="/examples/hello-world/demo/assemblyscript/"></iframe>

接下来让我们更深入地研究WebAssembly [Exports](/example-redirect?exampleName=exports).
