# #41 - Impossible to link with python (compiled with pymalloc) [Open]

> Username: hotgloupi  
> Created at: 2015-10-07 20:56:43 UTC  
> Updated at: 2015-10-07 20:56:43 UTC  
> Url: https://github.com/boostorg/python/issues/41  

Hi all,  
  
Today I'm using a configuration like that:  
  
```  
using python : 3.4  
             : /build/bin/python3.4  
             : /build/include/python3.4m  
             : /build/lib  
             ;  
```  
  
The problem lies in the fact that the library is `libpython3.4m.a` and Boost.Build is completely ignoring the `m` suffix:  
  
```  
    "g++" -dynamiclib -Wl,-single_module -install_name "libboost_python3.dylib" -L"/Users/Xcloud/sandbox/8cube/build/lib" -o "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/libboost_python3.dylib" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/numeric.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/list.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/long.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/dict.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/tuple.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/str.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/slice.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/converter/from_python.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/converter/registry.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/converter/type_id.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/object/enum.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/object/class.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/object/function.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/object/inheritance.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/object/life_support.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/object/pickle_support.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/errors.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/module.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/converter/builtin_converters.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/converter/arg_to_python_base.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/object/iterator.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/object/stl_iterator.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/object_protocol.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/object_operators.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/wrapper.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/import.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/exec.o" "bin.v2/libs/python/build/darwin-4.2.1/release/threading-multi/object/function_doc_signature.o"  -lpython3.4    -headerpad_max_install_names -Wl,-dead_strip -no_dead_strip_inits_and_terms  
  
ld: library not found for -lpython3.4  
```  
  
If I execute   
  
```  
/build/bin/python3.4 -c 'import distutils.sysconfig as s; print(s.get_config_var("LIBRARY"))'  
```  
  
it prints the correct library filename.  
  
Also, I tried to specify the `3.4m` version instead of `3.4` but it does nothing more than warn me about it:   
  
```  
 Warning: "using python" expects a two part (major, minor) version number; got 3.4m instead  
```  
  
Does anybody know how to link with python when it as been compiled with pymalloc ?  
Is there a workaround not involving a symlink ?
