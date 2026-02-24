# #670 - Build with emscripten toolchain fails [Open]

> Username: tibortakacs  
> Created at: 2022-07-14 21:17:14 UTC  
> Updated at: 2022-07-14 21:18:17 UTC  
> Url: https://github.com/boostorg/boost/issues/670  

Environment:  
Ubuntu 20.04 WSL2  
Boost version: 1.79.0  
Emscripten toolchain is downloaded as a nuget package and manually added to the PATH.  
  
Issue:  
  
I want to build `boost test` with `emscripten` toolchain. This is a `b2` command that almost works:  
  
`./b2 --address-model=32 --layout=tagged --build-type=minimal --build-dir=./build --with-test threading=multi toolset=emscripten variant=release`  
  
However, I get this error:  
`  
...  
emcc: error: '/home/user/.nuget/packages/emscripten-2.0.26-ubuntu-2004-x64/1.0.3/build/native/emsdk/upstream/bin/wasm-ld -o build/boost/bin.v2/libs/test/build/emscripten-2.0.26/release/threading-multi/visibility-hidden/libboost_unit_test_framework-mt.so.1.79.0 -h build/boost/bin.v2/libs/test/build/emscripten-2.0.26/release/threading-multi/visibility-hidden/compiler_log_formatter.bc build/boost/bin.v2/libs/test/build/emscripten-2.0.26/release/threading-multi/visibility-hidden/debug.bc build/boost/bin.v2/libs/test/build/emscripten-2.0.26/release/threading-multi/visibility-hidden/decorator.bc build/boost/bin.v2/libs/test/build/emscripten-2.0.26/release/threading-multi/visibility-hidden/execution_monitor.bc build/boost/bin.v2/libs/test/build/emscripten-2.0.26/release/threading-multi/visibility-hidden/framework.bc build/boost/bin.v2/libs/test/build/emscripten-2.0.26/release/threading-multi/visibility-hidden/plain_report_formatter.bc build/boost/bin.v2/libs/test/build/emscripten-2.0.26/release/threading-multi/visibility-hidden/progress_monitor.bc build/boost/bin.v2/libs/test/build/emscripten-2.0.26/release/threading-multi/visibility-hidden/results_collector.bc build/boost/bin.v2/libs/test/build/emscripten-2.0.26/release/threading-multi/visibility-hidden/results_reporter.bc build/boost/bin.v2/libs/test/build/emscripten-2.0.26/release/threading-multi/visibility-hidden/test_framework_init_observer.bc build/boost/bin.v2/libs/test/build/emscripten-2.0.26/release/threading-multi/visibility-hidden/test_tools.bc build/boost/bin.v2/libs/test/build/emscripten-2.0.26/release/threading-multi/visibility-hidden/test_tree.bc build/boost/bin.v2/libs/test/build/emscripten-2.0.26/release/threading-multi/visibility-hidden/unit_test_log.bc build/boost/bin.v2/libs/test/build/emscripten-2.0.26/release/threading-multi/visibility-hidden/unit_test_main.bc build/boost/bin.v2/libs/test/build/emscripten-2.0.26/release/threading-multi/visibility-hidden/unit_test_monitor.bc build/boost/bin.v2/libs/test/build/emscripten-2.0.26/release/threading-multi/visibility-hidden/unit_test_parameters.bc build/boost/bin.v2/libs/test/build/emscripten-2.0.26/release/threading-multi/visibility-hidden/junit_log_formatter.bc build/boost/bin.v2/libs/test/build/emscripten-2.0.26/release/threading-multi/visibility-hidden/xml_log_formatter.bc build/boost/bin.v2/libs/test/build/emscripten-2.0.26/release/threading-multi/visibility-hidden/xml_report_formatter.bc -Bstatic -Bdynamic -L/home/user/.nuget/packages/emscripten-2.0.26-ubuntu-2004-x64/1.0.3/build/native/emsdk/upstream/emscripten/cache/sysroot/lib/wasm32-emscripten --relocatable -mllvm -combiner-global-alias-analysis=false -mllvm -enable-emscripten-sjlj -mllvm -disable-lsr' failed (returned 1)  
...  
`  
  
As you can see, the building framework calls `wasm-ld` with `-h ...`, `-Bstatic` and `-Bdynamic` parameters which are not supported by `wasm-ld`. If I directly call the long command above after removing these parameters first, the command succeeds.  
  
Is this expected? Should I modify the building framework to call `wasm-ld` with the correct parameters?
