# #382 - "bootstrap.bat borland" fails to find label "Test_Path" [Closed]

> Username: tanzislam  
> Created at: 2019-01-06 19:27:45 UTC  
> Updated at: 2019-02-27 01:32:57 UTC  
> Closed at: 2019-02-27 01:32:57 UTC  
> Url: https://github.com/boostorg/build/issues/382  

I have both VS2017 and Embarcadero C++Builder 10.3 (i.e. the `borland` toolchain) installed in my Windows 7 SP1 (64-bit) laptop. I freshly cloned the Boost super-project with all submodules (`git submodule update --init --recursive`), then tried building Boost.Build specifically with the `borland` toolchain in a plain `cmd.exe` window:  
  
```  
>bootstrap.bat borland  
```  
  
This failed with:  
  
```  
Building Boost.Build engine  
The system cannot find the batch label specified - Test_Path  
  
Failed to build Boost.Build engine.  
Please consult bootstrap.log for further diagnostics.  
```

---

## Comment 1

> Username: tanzislam  
> Created at: 2019-01-06 19:40:56 UTC  
> Updated at: 2019-01-07 02:15:59 UTC  
> Url: https://github.com/boostorg/build/issues/382#issuecomment-451768601  

I believe the reason is that the `Test_Path` label exists inside `src/engine/guess_toolset.bat`, but that script is skipped in the call path (`bootstrap.bat` -> `src/engine/build.bat` -> `src/engine/config_toolset.bat`) if a toolset argument is provided. The error occurs at the `:Skip_VC141` logic in `config_toolset.bat` when it calls `Test_Path`.  
  
I have a working patch prepared; getting my employer's permission before raising a PR ...
