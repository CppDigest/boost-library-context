# #776 - Option to turn off wasm-exceptions for emscripten builds not available [Closed]

> Username: dsamo  
> Created at: 2024-07-05 07:14:32 UTC  
> Updated at: 2024-07-05 07:14:46 UTC  
> Closed at: 2024-07-05 07:14:45 UTC  
> Url: https://github.com/boostorg/build/issues/776  

Hello, in our company we use boost builds in a webassembly client using emscripten.   
  
Since boost 1.85 it seems by looking into the emscripten jam file that there is no way to turn off wasm-exceptions unless we use node < 16 or emscripten major version < 2.   
Currently our wasm client is still using js exceptions and if we are to transition we need to put time there to investigate certain things. But with the restriction above we now have a client being built with js exceptions and a library (boost) being built with wasm-exceptions which leads to undefined symbols linker error for `__wasm_lpad_context`.  
  
Is there a feature flag to circumvent this explicitly?

---

## Comment 1

> Username: github-actions[bot]  
> Created at: 2024-07-05 07:14:44 UTC  
> Url: https://github.com/boostorg/build/issues/776#issuecomment-2210328110  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/issues
