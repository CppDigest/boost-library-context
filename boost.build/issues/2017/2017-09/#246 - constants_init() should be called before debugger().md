# #246 - constants_init() should be called before debugger() [Closed]

> Username: hia3  
> Created at: 2017-09-29 15:36:58 UTC  
> Updated at: 2017-12-06 01:26:58 UTC  
> Closed at: 2017-12-06 01:26:58 UTC  
> Url: https://github.com/boostorg/build/issues/246  

If `constants_init()` is not called before `debugger()`, it can fail in `debug_print_source()` because `constant_builtin` is not initialized.  
  
To reproduce, do `bootstrap --debug`, `b2 -dconsole`, `break path.join`, `run`.  
  
    Assertion failed: obj, file object.c, line 242
