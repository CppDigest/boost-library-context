# #402 - WebAssembly/Emscripten support witth pthread failing (BOOST_HAS_PTHREADS not defined when it should be) [Closed]

> Username: Klaim  
> Created at: 2021-09-08 14:25:00 UTC  
> Updated at: 2021-09-10 10:41:22 UTC  
> Closed at: 2021-09-10 10:36:29 UTC  
> Url: https://github.com/boostorg/config/issues/402  

Might be relatd to #400   
  
## Observed  
  
To be short: when using Emscripten to build boost packages that needs pthread, we pass `-pthread` or `-s USE_PTHREADS=1` to the compiler. However **this does NOT result in `BOOST_HAS_PTHREADS`** being defined, which leads to compilation errors when compiling boost libraries using threading APIS, like for example with `boost-container` which ends up trying to use Window's mutex api.  
  
Here is an example from the [`build2`](https://build2.org/) CI trying to compil `boost-container` with `Emscripten` (here `-pthread` is not set but the exact same error appears with it set anyway) :  https://queue.stage.build2.org/libboost-container/1.77.0-a.0.20210903124838.904d91383efa[…].0.25/stage/0.14.0-a.0.20210906090406.d7f7472f9ac3/update  
  
I checked in a hello-world project and confirm that setting `-pthread` using `em++` will not result in `BOOST_HAS_PTHREADS` being defined after including `<boost/config.hpp>`.  
  
## Expected  
  
boost-config should define `BOOST_HAS_PTHREADS` when `-pthread` is set using `em++` and `emcc`.  
  
## Workaround  
  
My current workaround is to set ("force") `-DBOOST_HAS_PTHREADS` but that's a hack, it should be defined by `boost-config`.

---

## Comment 1

> Username: boris-kolpackov  
> Created at: 2021-09-09 07:05:25 UTC  
> Url: https://github.com/boostorg/config/issues/402#issuecomment-915820688  

BTW, according to https://emscripten.org/docs/porting/pthreads.html:  
  
> In C/C++ code, the preprocessor check `#ifdef __EMSCRIPTEN_PTHREADS__` can be used to detect whether Emscripten is currently targeting pthreads.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-09-09 10:35:02 UTC  
> Url: https://github.com/boostorg/config/issues/402#issuecomment-915967435  

Thanks for the info, I probably need to figure out how to get an EMScripten run on our CI, otherwise things may regress down the road :(

---

## Comment 3

> Username: Klaim  
> Created at: 2021-09-09 12:35:49 UTC  
> Url: https://github.com/boostorg/config/issues/402#issuecomment-916051883  

We can probably help.   
The recommended way to install Emscripten is through their github, see the instructions there: https://emscripten.org/docs/getting_started/downloads.html  
In our CI we have a script just following this (we use the latest version always).  
  
Then once you have run the `source source ./emsdk_env.sh` (or bat on windows) you can use `emcc` and `em++` like if they were `clang++` and `clang`.   
The main differences:  
- by default `emcc/++` will output WebAssembly files, which means 1 js file and 1 wasm file, which is loaded by the js file. You can test that it runs using the `node` provided in the Emscripten install (it should be just `node` once you did the source step);  
- when adding `-pthread` (in both preprocessing and linking steps), there will be an additional `<target>.worker.js` file generated, which needs to be next to the other files. This one exists because threading is implemented using JavaScript's Worker system. `node` can handle it too if you run it with `--experimental-wasm-threads`. For building tests, you will probably also need to set these link options `-s EXIT_RUNTIME=1` and `-s PTHREAD_POOL_SIZE=8` or any appropriate number;  
- to run the tests (if you have some to run), just run them through node: `node --experimental-wasm-threads mytests.js`, it will run the wasm and return once the `main()` is done (if you did set `-s EXIT_RUNTIME=1` at link -time).  
  
I suppose you will have to setup `b2` to handle that? Hopefully the fact that Emscripten is basically a twist on clang will help.

---

## Comment 4

> Username: Klaim  
> Created at: 2021-09-09 14:10:35 UTC  
> Url: https://github.com/boostorg/config/issues/402#issuecomment-916135023  

More info: https://web.dev/webassembly-threads/

---

## Comment 5

> Username: jzmaddock  
> Created at: 2021-09-09 19:18:56 UTC  
> Url: https://github.com/boostorg/config/issues/402#issuecomment-916370239  

OK, defining BOOST_HAS_UNISTD_H is sufficient to fix missing thread detection.  
  
However, while config_test.cpp builds, it hangs when run under node.  
  
I'm using:  
  
```  
emcc -pthread -s EXIT_RUNTIME=1 -s PTHREAD_POOL_SIZE=8 -s DISABLE_EXCEPTION_CATCHING=0 -I. -O3 -o config_test_pthread libs/config/config_test.cpp  
node --experimental-wasm-threads --experimental-wasm-bulk-memory config_test_pthread  
```  
  
From the Boost root directory rather than messing around with b2 for this simple case.  
  
Any ideas?

---

## Comment 6

> Username: jzmaddock  
> Created at: 2021-09-09 19:39:22 UTC  
> Url: https://github.com/boostorg/config/issues/402#issuecomment-916382492  

It does seem to work on Linux though...

---

## Comment 7

> Username: Klaim  
> Created at: 2021-09-09 22:38:40 UTC  
> Url: https://github.com/boostorg/config/issues/402#issuecomment-916488138  

Here are the flags I ended up using in my threading-on-wasm tests (I experimented more today), variations lead to expected hang-up: https://github.com/Klaim/build2-wasm-threading/blob/main/config-wasm.options  
(this is using `build2`, `*.poptions` are pre-processor options, `*.coptions` are compilation options (which is separate in `build2`) and `*.loptions` are linker options)  
  
One possibility is that you actually spawn more than 8 threads, that would lead to a deadlock (see emscripten pthread doc).  
The other possibility is because the main thread is run in the node or browser mainthread, leading to locking issues (there is also a section about this somewhere)  
To work around that you can use `-s PROXY_TO_PTHREAD` so that the main thread (like other threads) is implemented as a js worker too. Unfortunately Emscripten adds some logs at the end of the program, I intend to send an issue to them about that (couldn't redact it yet).  
  
Another thing I found today is that you can actually make the number of threads grow (as long as you don't need to optimize to the max) using `-s PTHREAD_POOL_SIZE_STRICT=0`.   
  
Most of the other flags I used are there to remove warnings and allow memory growth.

---

## Comment 8

> Username: jzmaddock  
> Created at: 2021-09-10 10:41:22 UTC  
> Url: https://github.com/boostorg/config/issues/402#issuecomment-916808171  

My bad, I didn't have -s PTHREAD_POOL_SIZE correct when testing on windows.  
  
I've merged fixes to develop now.
