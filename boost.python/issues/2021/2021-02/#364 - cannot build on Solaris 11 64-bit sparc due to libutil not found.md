# #364 - cannot build on Solaris 11 64-bit sparc due to libutil not found [Open]

> Username: codenaugh  
> Created at: 2021-02-18 18:40:28 UTC  
> Updated at: 2021-11-03 17:50:34 UTC  
> Url: https://github.com/boostorg/python/issues/364  

My understanding is that Solaris has no such library, but boost does not have a check to determine whether or not to link it. It just always does.  
```  
gcc.link.dll bin.v2/libs/python/build/gcc-10.2.0/release/python-2.7/threading-multi/visibility-hidden/libboost_python27.so.1.75.0  
ld: fatal: library -lutil: not found  
collect2: error: ld returned 1 exit status  
  
    "g++"    -o "bin.v2/libs/python/build/gcc-10.2.0/release/python-2.7/threading-multi/visibility-  
hidden/libboost_python27.so.1.75.0" -Wl,-h -Wl,libboost_python27.so.1.75.0 -shared -Wl,--start-group   
"bin.v2/libs/python/build/gcc-10.2.0/release/python-2.7/threading-multi/visibility-hidden/list.o" "bin.v2/libs/python/build/gcc-  
10.2.0/release/python-2.7/threading-multi/visibility-hidden/long.o" "bin.v2/libs/python/build/gcc-10.2.0/release/python-  
2.7/threading-multi/visibility-hidden/dict.o" "bin.v2/libs/python/build/gcc-10.2.0/release/python-2.7/threading-multi/visibility-  
hidden/tuple.o" "bin.v2/libs/python/build/gcc-10.2.0/release/python-2.7/threading-multi/visibility-hidden/str.o"   
"bin.v2/libs/python/build/gcc-10.2.0/release/python-2.7/threading-multi/visibility-hidden/slice.o" "bin.v2/libs/python/build/gcc-  
10.2.0/release/python-2.7/threading-multi/visibility-hidden/converter/from_python.o" "bin.v2/libs/python/build/gcc-  
10.2.0/release/python-2.7/threading-multi/visibility-hidden/converter/registry.o" "bin.v2/libs/python/build/gcc-  
10.2.0/release/python-2.7/threading-multi/visibility-hidden/converter/type_id.o" "bin.v2/libs/python/build/gcc-  
10.2.0/release/python-2.7/threading-multi/visibility-hidden/object/enum.o" "bin.v2/libs/python/build/gcc-  
10.2.0/release/python-2.7/threading-multi/visibility-hidden/object/class.o" "bin.v2/libs/python/build/gcc-10.2.0/release/python-  
2.7/threading-multi/visibility-hidden/object/function.o" "bin.v2/libs/python/build/gcc-10.2.0/release/python-2.7/threading-  
multi/visibility-hidden/object/inheritance.o" "bin.v2/libs/python/build/gcc-10.2.0/release/python-2.7/threading-multi/visibility-  
hidden/object/life_support.o" "bin.v2/libs/python/build/gcc-10.2.0/release/python-2.7/threading-multi/visibility-  
hidden/object/pickle_support.o" "bin.v2/libs/python/build/gcc-10.2.0/release/python-2.7/threading-multi/visibility-  
hidden/errors.o" "bin.v2/libs/python/build/gcc-10.2.0/release/python-2.7/threading-multi/visibility-hidden/module.o"   
"bin.v2/libs/python/build/gcc-10.2.0/release/python-2.7/threading-multi/visibility-hidden/converter/builtin_converters.o"   
"bin.v2/libs/python/build/gcc-10.2.0/release/python-2.7/threading-multi/visibility-hidden/converter/arg_to_python_base.o"   
"bin.v2/libs/python/build/gcc-10.2.0/release/python-2.7/threading-multi/visibility-hidden/object/iterator.o"   
"bin.v2/libs/python/build/gcc-10.2.0/release/python-2.7/threading-multi/visibility-hidden/object/stl_iterator.o"   
"bin.v2/libs/python/build/gcc-10.2.0/release/python-2.7/threading-multi/visibility-hidden/object_protocol.o"   
"bin.v2/libs/python/build/gcc-10.2.0/release/python-2.7/threading-multi/visibility-hidden/object_operators.o"   
"bin.v2/libs/python/build/gcc-10.2.0/release/python-2.7/threading-multi/visibility-hidden/wrapper.o"   
"bin.v2/libs/python/build/gcc-10.2.0/release/python-2.7/threading-multi/visibility-hidden/import.o"   
"bin.v2/libs/python/build/gcc-10.2.0/release/python-2.7/threading-multi/visibility-hidden/exec.o" "bin.v2/libs/python/build/gcc-  
10.2.0/release/python-2.7/threading-multi/visibility-hidden/object/function_doc_signature.o"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -  
ldl -lpthread -lutil -Wl,--end-group -fPIC -m64 -pthread -fvisibility=hidden -fvisibility-inlines-hidden -mcpu=v7  
  
...failed gcc.link.dll bin.v2/libs/python/build/gcc-10.2.0/release/python-2.7/threading-multi/visibility-  
hidden/libboost_python27.so.1.75.0...  
```

---

## Comment 1

> Username: alyssais  
> Created at: 2021-06-01 16:48:39 UTC  
> Updated at: 2021-06-01 16:49:23 UTC  
> Url: https://github.com/boostorg/python/issues/364#issuecomment-852280671  

I have the same problem on Tribblix (illumos).  I think the problem is here:  
  
https://github.com/boostorg/build/blob/76da80f33187a3d9e5336157cdfae12ce82e37eb/src/tools/python.jam#L628  
  
In my case, rather than something like "solaris" or "sunos", target-os is "unix", which doesn't seem right.  I imagine that would fall through to the default case, and then try to link with libutil because I'm using gcc.

---

## Comment 2

> Username: alyssais  
> Created at: 2021-06-01 18:26:50 UTC  
> Url: https://github.com/boostorg/python/issues/364#issuecomment-852349877  

pkgsrc has also [encountered](http://cvsweb.netbsd.org/bsdweb.cgi/~checkout~/pkgsrc/devel/py-boost/Makefile?rev=1.12&content-type=text/x-cvsweb-markup) this problem, it looks like.  They just hack around it.

---

## Comment 3

> Username: codenaugh  
> Created at: 2021-11-03 17:13:37 UTC  
> Url: https://github.com/boostorg/python/issues/364#issuecomment-959745387  

I ended up hacking around it as well by adding `case uni* : return <toolset>gcc:<library>rt ;` above the sun/solaris `target_os` check on line 630 of `tools/build/src/tools/python.jam`

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2021-11-03 17:45:08 UTC  
> Url: https://github.com/boostorg/python/issues/364#issuecomment-959773316  

I think this would be best reported to the boost.build project, which maintains this logic.  
I'm unfortunately not familiar enough to offer advise, or even provide patches. Thanks !

---

## Comment 5

> Username: codenaugh  
> Created at: 2021-11-03 17:49:12 UTC  
> Updated at: 2021-11-03 17:49:54 UTC  
> Url: https://github.com/boostorg/python/issues/364#issuecomment-959776694  

Well that's where it was originally reported, and they moved it to boost/python. The issue stems from the `target_os` variable getting set to `unix` instead of `solaris` or `sunos`. This causes it to match the last case instead of the first one in `tools/build/src/tools/python.jam`, which adds `libutil` to the link tree and libutil ddoesn't exist on solaris. I was unable to figure out where and how the `target_os` variable is being set incorrectly, as that's the real fix.

---

## Comment 6

> Username: codenaugh  
> Created at: 2021-11-03 17:50:34 UTC  
> Url: https://github.com/boostorg/python/issues/364#issuecomment-959777828  

That being said, I do not mind if it gets moved back to `boost/build`
