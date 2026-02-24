# #526 - Compiling with emscripten missing linkflags in jam file [Closed]

> Username: SmajeNz0  
> Created at: 2020-01-11 21:30:36 UTC  
> Updated at: 2023-07-18 02:53:08 UTC  
> Closed at: 2023-07-18 02:53:07 UTC  
> Url: https://github.com/boostorg/build/issues/526  

Hello  
  
I needed to compile some boost libs using emscripten, which use phthread, like the atomic one.  
  
According to the emscripten docs I needed to pass the following flag:  "-s USE_PTHREADS=1"  
  
```  
./b2 toolset=emscripten cxxflags="-s USE_PTHREADS=1"  cflags="-s USE_PTHREADS=1" --with-atomic link=static runtime-link=static threading=multi --disable-icu  
```  
  
I tried with cxxflags, cflags and linksflag as well, but it had no effect at all. The linker always complained about the missing --shared-memory flag.  
  
Finally I manually added the flag here: https://github.com/boostorg/build/blob/ab5ce1e4686715bc83eeb28b809b9268d6cb2f63/src/tools/emscripten.jam#L105 and it compiled successfully with the following command:  
```  
./b2 toolset=emscripten --with-atomic link=static runtime-link=static threading=multi --disable-icu  
```  
Is it the normal behaviour that only *.bc files are being compiled?  
  
Why are the flags passed to emcc at the AR stage with the jam file?  
  
Thank you

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 18:22:04 UTC  
> Url: https://github.com/boostorg/build/issues/526#issuecomment-850877326  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
