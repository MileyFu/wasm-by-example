# 导出

## 概览


在我们的[ Hello World Example ](/ Example-redirect？ 例如 hello-world) ，我们在 Javascript 中调用从 WebAssembly 导出的函数。**然而，你可以将函数和常量导出到 Javascript 中。** 让我们更深入地探讨导出以及它们的使用方式。

---

## 实施

首先让我们创建  `exports.ts` AssemblyScript文件:

```typescript
// 导出一个 add 函数。

// 它接受两个32位整数值

// 并返回一个32位的整数值。

导出函数 callMeFromJavascript(a: i32, b: i32): i32 {
  返回 addIntegerWithConstant(a, b);
}

// 这将导出一个32位的整数常量


/ / a 


/ / 并返回一个32位的整数值。

函数
export const GET_THIS_CONSTANT_FROM_JAVASCRIPT: i32 = 2424;

// 一个未导出函数
// 它拿了两个32位的整数值
// 并返回一个32位的整数值
函数 addIntegerWithConstant(a: i32, b: i32): i32 {
  返回 a + b + ADD_CONSTANT;
}

// A NOT export constant
// a 32-bit integer constants
const ADD_CONSTANT: i32 = 1;
```

然后我们将它编译成 wasm 模块, 用 [AssemblyScript 编译器](https://docs.assemblyscript.org/details/compiler), 它会输出： `export-function.wasm`:

```bash
asc exports.ts -b exports.wasm
```

接下来，我们来加载/实例化 wasm模块, `export-function.wasm` 在一个新的 `export-function.js` 文件。 看这个模块 [WebAssembly Module Documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/WebAssembly/Module), We see that after instantiation of our module我们发现在实例化我们的模块之后, 我们的模块有了 `.instance` 属性, 它是一个 [WebAssembly模块实例](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/WebAssembly/Instance). 实例化模块的例子可以在这里找到 [Hello World Example](/example-redirect?exampleName=hello-world）。最后我们看到我们的实例 `.exports` 属性。 `.exports` 属性是一个包含所有导出的函数 / 常量的对象，这些函数 / 常量可以通过 Javascript 同步调用 / 访问。 函数可以使用正常的函数语法调用，而常量则需要使用`.valueOf()来访问。 取决于他们是否作为[ WebAssembly Globals ]的导出 [WebAssembly Globals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly/Global). 下面是一些片段:

```javascript
const runWasm = async () => {
  // 实例化我们的wasm模块
  const wasmModule = await wasmBrowserInstantiate("./exports.wasm");

  // 获取我们的 exports 对象，以及所有导出的 Wasm 属性
  const exports = wasmModule.instance.exports;

  console.log(exports.callMeFromJavascript(24, 24)); // 日志 49

  // 因为我们的常量是一个全局变量，所以我们使用 `.valueOf()`.
  //虽然，在一些情况下这个可以仅为: exports.GET_THIS_CONSTANT_FROM_JAVASCRIPT
  console.log(exports.GET_THIS_CONSTANT_FROM_JAVASCRIPT.valueOf()); // 日志 2424

  // Trying to access a property we did NOT export
  console.log(exports.addIntegerWithConstant); // 日志未定义
};
runWasm();
```

最后，让我们加载 ES6 模块, `exports.js` Javascript文件到我们的`index.html`.你就能得到类似下面 demo的结果 ([Source Code](/source-redirect?path=examples/exports/demo/assemblyscript)) !

---

## Demo

<iframe title="AssemblyScript Demo" src="/examples/exports/demo/assemblyscript/"></iframe>

下面我们来看WebAssembly线性存储 [WebAssembly Linear Memory](/example-redirect?exampleName=webassembly-linear-memory).
