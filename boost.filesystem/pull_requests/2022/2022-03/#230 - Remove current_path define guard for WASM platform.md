# #230 Remove current_path define guard for WASM platform [Closed]

> Username: DSchroer  
> Created at: 2022-03-06 17:01:25 UTC  
> Updated at: 2022-03-07 19:29:16 UTC  
> Closed at: 2022-03-07 19:29:16 UTC  
> Url: https://github.com/boostorg/filesystem/pull/230  

Some WASM runtimes support the operations that are hidden behind these guards. Expose the functionality to the WASM platform.

---

## Comment 1

> Username: Lastique  
> Created_at: 2022-03-06 22:40:21 UTC  
> Url: https://github.com/boostorg/filesystem/pull/230#issuecomment-1060053344  

The change was made as part of https://github.com/boostorg/filesystem/pull/144 by @whitequark. There, it was reported that WASI does not support an absolute current path.  
  
> Some WASM runtimes support...  
  
If there are multiple different runtimes, some supporting the current path, others not, then this change is not correct. The library should work on all runtimes, so it must detect if the runtime supports the operation. If this is not possible then a user-defined config macro could be added. I don't know anything about WASI, so I can't pick a reasonable default for such a macro, but I lean towards "disabled by default" as that would make the library more portable by default.

---

## Comment 2

> Username: DSchroer  
> Created_at: 2022-03-07 14:58:37 UTC  
> Url: https://github.com/boostorg/filesystem/pull/230#issuecomment-1060778408  

The issue here is that the compiler toolchains like `emscripten` have already decided to support current path. So if you compile code using `std::filesystem::current_path` it will work as intended. However if I use `boost::filesystem::current_path` it will fail unexpectedly.   
  
WASM is a very strange platform because really the platform is lightweight and software defined. If you prefer to hide it behind another flag I am happy to do so but locking it off because WASI does not support it while WASM can easily support it does not make a ton of sense to me.

---

## Comment 3

> Username: Lastique  
> Created_at: 2022-03-07 15:08:46 UTC  
> Url: https://github.com/boostorg/filesystem/pull/230#issuecomment-1060789762  

My understanding is that WASI to WASM is similar to libc to C. I really have no idea how "standard" WASI is in the WASM ecosystem.  
  
My rationale is that Boost.Filesystem should work in the "standard" WASM environment, for whatever definition of "standard" there is in the WASM ecosystem. If there are extensions to the standard provided by some environments, those extensions must be detected (at compile or run time) or hidden behind the user-definable switch. Making them mandatory, effectively breaking compatibility with some valid implementations, is not the way to go.

---

## Comment 4

> Username: DSchroer  
> Created_at: 2022-03-07 15:37:05 UTC  
> Url: https://github.com/boostorg/filesystem/pull/230#issuecomment-1060820781  

To me there is another layer between. WASI is the OS that libc runs on. There is already a WASI-libc library that exists to run in the WASI ecosystem. And that lib has support for current directory: https://github.com/WebAssembly/wasi-libc/pull/214

---

## Comment 5

> Username: Lastique  
> Created_at: 2022-03-07 17:30:30 UTC  
> Url: https://github.com/boostorg/filesystem/pull/230#issuecomment-1060941897  

Is there a way to detect if these APIs are supported by libc?

---

## Comment 6

> Username: DSchroer  
> Created_at: 2022-03-07 17:37:19 UTC  
> Url: https://github.com/boostorg/filesystem/pull/230#issuecomment-1060948032  

> Is there a way to detect if these APIs are supported by libc?  
  
Not that I know of but someone more well versed in this domain might know. From my understanding the only way to know if an API is available in WASM is at runtime. Its a user defined sandbox environment so the runtime env and the compiletime env are often very different.   
  
If the build succeeds using emcc and there are no warnings regarding exported APIs being missing, its a pretty good guess that everything will be available.

---

## Comment 7

> Username: DSchroer  
> Created_at: 2022-03-07 17:46:19 UTC  
> Url: https://github.com/boostorg/filesystem/pull/230#issuecomment-1060956608  

Keep in mind the WASM is always statically linked. So the risk of backwards compatibility is much less on this platform.

---

## Comment 8

> Username: Lastique  
> Created_at: 2022-03-07 17:50:05 UTC  
> Url: https://github.com/boostorg/filesystem/pull/230#issuecomment-1060960045  

So, even a configure-time check won't work?

---

## Comment 9

> Username: DSchroer  
> Created_at: 2022-03-07 17:53:10 UTC  
> Url: https://github.com/boostorg/filesystem/pull/230#issuecomment-1060962899  

Not really. You only know if the platform can support it when the final file is run. I can take a `.wasm` file and run it on two completely different WASI runtimes. For example NodeJS and Chrome. The wasm binary will have no way to detect that. If the symbol is missing the system will refuse to run the wasm binary but the user can then define the symbol and try again without recompiling anything.

---

## Comment 10

> Username: DSchroer  
> Created_at: 2022-03-07 17:55:35 UTC  
> Updated_at: 2022-03-07 17:56:42 UTC  
> Url: https://github.com/boostorg/filesystem/pull/230#issuecomment-1060964997  

This PR does not even define or import any new symbols. Its just making sure that _wasm does not go directly to:   
```  
emit_error(BOOST_ERROR_NOT_SUPPORTED  
```  
  
On WASI platforms that it does not work for it will continue to not work.  
On WASI platforms that it does work for it will now start to work.

---

## Comment 11

> Username: Lastique  
> Created_at: 2022-03-07 19:27:21 UTC  
> Url: https://github.com/boostorg/filesystem/pull/230#issuecomment-1061051526  

The difference is that with this patch the file will fail to load due to a missing symbol, even if it isn't used.  
  
If there is no way to detect support for current directory API then I'm inclined to leave it unsupported by default. An opt-in macro to enable it on user's request seems to be the only way forward, but this PR is not that.

---

## Comment 12

> Username: DSchroer  
> Created_at: 2022-03-07 19:29:16 UTC  
> Url: https://github.com/boostorg/filesystem/pull/230#issuecomment-1061053295  

Ill close it for now however I suspect that this missing symbol still wont occur on any of the WASI runtimes now. Until then we will just leave the patch in our project. https://github.com/DSchroer/openscad-wasm

---
