# #233 Implement on top of posix API for emscripten [Closed]

> Username: guusw  
> Created at: 2022-04-25 11:25:24 UTC  
> Updated at: 2022-05-15 17:57:33 UTC  
> Closed at: 2022-05-15 17:57:33 UTC  
> Url: https://github.com/boostorg/filesystem/pull/233  

I want to add a set of defines that correctly implement this library when compiling on top of emscripten.  
  
Some explanation about the checked defines:  
  
- ~~The `EMSCRIPTEN_STANDALONE_WASM` is defined when you build with `-s STANDALONE_WASM` so that case should still be ignored/left unimplemented~~  
  
- ~~When the above is not set and `__EMSCRIPTEN__` is defined, it's safe to assume that the js library is bundled that implemented the posix filesystem API.~~  
  
> Apparently `EMSCRIPTEN_STANDALONE_WASM` is only set during linking so it's not useful in this case  
  
I saw  https://github.com/boostorg/filesystem/pull/230 , which seems to want to do the same but this should work for both the standalone/emscripten case

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2022-04-25 12:15:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/233#pullrequestreview-951766384  

📁 src/operations.cpp

```diff
  40 | 
  41 |+ #if defined(__wasm) && (defined(EMSCRIPTEN_STANDALONE_WASM) || !defined(__EMSCRIPTEN__))
  42 |+ #define BOOST_STANDALONE_WASM 1
```

> Username: Lastique  
> Created_at: 2022-04-25 12:15:55 UTC  
> Url: https://github.com/boostorg/filesystem/pull/233#discussion_r857561317  

Could you rename this to `BOOST_FILESYSTEM_STANDALONE_WASM`?


---

## Comment 2

> Username: Lastique  
> Created_at: 2022-04-25 12:21:15 UTC  
> Url: https://github.com/boostorg/filesystem/pull/233#issuecomment-1108500332  

Also, can you confirm that this change will work with WASI?

---

## Comment 3

> Username: guusw  
> Created_at: 2022-04-25 14:41:25 UTC  
> Url: https://github.com/boostorg/filesystem/pull/233#issuecomment-1108666479  

Apparently `EMSCRIPTEN_STANDALONE_WASM` is only set during linking so it's not useful in this case.  
  
I added an alternative mechanism where configuring with `BOOST_FILESYSTEM_DISABLE_EMSCRIPTEN_WASI=ON` will use the posix API, otherwise it keeps working as it is now.  
  
I would prefer it to be the other way around though since it's an opt-in from emscripten's side too (`-sSTANDALONE_WASM` during linking) although current WASI users will have to add this option.

---

## Review 4 [Commented]

> Username: Lastique  
> Created_at: 2022-04-27 09:27:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/233#pullrequestreview-954593479  

📁 src/operations.cpp

```diff
  39 | #include <cerrno>
  40 | 
  41 |+ // Use WASI when not building with emscripten or when BOOST_FILESYSTEM_DISABLE_EMSCRIPTEN_WASI is set
```

> Username: Lastique  
> Created_at: 2022-04-27 09:27:25 UTC  
> Url: https://github.com/boostorg/filesystem/pull/233#discussion_r859581244  

"when BOOST_FILESYSTEM_DISABLE_EMSCRIPTEN_WASI is *not* set"?


---

## Comment 5

> Username: Lastique  
> Created_at: 2022-04-27 09:36:14 UTC  
> Url: https://github.com/boostorg/filesystem/pull/233#issuecomment-1110786415  

I have next to zero knowledge about WebAssembly and related ecosystem, so may I ask a few questions for my own education to better understand the PR?  
  
I don't quite understand where Emscripten is wrt. WASI. Is WASI using Emscripten as a compiler or is there a different compiler that happen to define `__wasm`?  
  
If Emscripten is the only compiler, and WASI is just one alternative of the system API, along with what Emscripten provides (what you called "JavaScript API"), which one is the default? Can we assume the default at all?  
  
The reason I'm asking is I'd like to know which is the most reasonable default target to compile for. In other words, is the full POSIX API "the norm" or not in WebAssembly world? Or are Emscripten and WASI completely independent and should be considered separate target platforms?

---

## Comment 6

> Username: guusw  
> Created_at: 2022-04-27 13:34:11 UTC  
> Url: https://github.com/boostorg/filesystem/pull/233#issuecomment-1111010420  

Out of the box you use the Emscripten compiler to generate a set of Wasm/JS files for running in a browser.  
The C++ code is compiled into the wasm module and imports some functionality defined in the JavaScript file (for example filesystem).  
  
In addition to building for the web, Emscripten also has an `-sSTANDALONE_WASM` compiler flag which causes it to not generate JavaScript code an instead build just a wasm module.  
  
Emscripten tries to use WASI for some of the basic functionality (open/read/write) so if you build using `STANDALONE_WASM` you could run it on any WASI runtime.  
  
Additionaly the [WASI-SDK](https://github.com/WebAssembly/wasi-sdk) exists - another compiler/SDK - which builds purely  against WASI  
  
I'm not to familiar with the current state of WASI but currently Emscripten implements most of the filesystem API through the POSIX functions (getcwd/stat/etc.)

---

## Comment 7

> Username: Lastique  
> Created_at: 2022-04-27 14:52:21 UTC  
> Url: https://github.com/boostorg/filesystem/pull/233#issuecomment-1111101021  

> Out of the box you use the Emscripten compiler to generate a set of Wasm/JS files for running in a browser. The C++ code is compiled into the wasm module and imports some functionality defined in the JavaScript file (for example filesystem).  
>   
> In addition to building for the web, Emscripten also has an `-sSTANDALONE_WASM` compiler flag which causes it to not generate JavaScript code an instead build just a wasm module.  
>   
> Emscripten tries to use WASI for some of the basic functionality (open/read/write) so if you build using `STANDALONE_WASM` you could run it on any WASI runtime.  
  
So, if I understand it right, by default Emscripten targets JavaScript API that supports POSIX. `STANDALONE_WASM` is an option that the user has to manually select.  
  
Is it possible to follow this convention in Boost.Filesystem? I.e. when Emscripten is used, by default assume full POSIX compliance, and add a user-defined macro to enable targeting `STANDALONE_WASM`. And while doing this, keep WASI-SDK (with its own compiler) functional by default.  
  
Basically, can we separate Emscripten and WASI-SDK compiler and use different defaults for them?

---

## Comment 8

> Username: guusw  
> Created_at: 2022-04-27 15:50:19 UTC  
> Url: https://github.com/boostorg/filesystem/pull/233#issuecomment-1111165954  

>So, if I understand it right, by default Emscripten targets JavaScript API that supports POSIX. STANDALONE_WASM is an option that the user has to manually select.  
  
Yes  
  
> Basically, can we separate Emscripten and WASI-SDK compiler and use different defaults for them?  
  
Pretty much what I wanted to do. I will update the PR and ping you when it's done

---

## Comment 9

> Username: guusw  
> Created_at: 2022-05-04 16:52:51 UTC  
> Url: https://github.com/boostorg/filesystem/pull/233#issuecomment-1117581180  

Updated it.  
Wasn't sure what to name it so I went with `BOOST_FILESYSTEM_EMSCRIPTEN_USE_WASI ` for the cmake option

---

## Review 10 [Commented]

> Username: Lastique  
> Created_at: 2022-05-05 20:35:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/233#pullrequestreview-963927151  

📁 CMakeLists.txt

```diff
 133 |     target_compile_definitions(boost_filesystem PRIVATE BOOST_FILESYSTEM_DISABLE_BCRYPT)
 134 | endif()
 135 |+ if(BOOST_FILESYSTEM_DISABLE_EMSCRIPTEN_WASI)
```

> Username: Lastique  
> Created_at: 2022-05-05 20:35:45 UTC  
> Url: https://github.com/boostorg/filesystem/pull/233#discussion_r866293393  

This block looks outdated.


---

## Review 11 [Commented]

> Username: Lastique  
> Created_at: 2022-05-05 20:37:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/233#pullrequestreview-963928879  

📁 CMakeLists.txt

```diff
  20 | set(BOOST_FILESYSTEM_DISABLE_ARC4RANDOM OFF CACHE BOOL "Disable usage of arc4random API in Boost.Filesystem")
  21 | set(BOOST_FILESYSTEM_DISABLE_BCRYPT OFF CACHE BOOL "Disable usage of BCrypt API in Boost.Filesystem")
  22 |+ set(BOOST_FILESYSTEM_EMSCRIPTEN_USE_WASI OFF CACHE BOOL "Use WASI under emscripten in Boost.Filesystem")
```

> Username: Lastique  
> Created_at: 2022-05-05 20:37:31 UTC  
> Url: https://github.com/boostorg/filesystem/pull/233#discussion_r866294590  

Shouldn't "Emscripten" start with a capital E?


---

## Comment 12

> Username: Lastique  
> Created_at: 2022-05-15 17:57:33 UTC  
> Url: https://github.com/boostorg/filesystem/pull/233#issuecomment-1126987176  

Thanks. I've merged a modified version of this PR.

---
