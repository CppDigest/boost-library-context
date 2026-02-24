# #234 - Crash when FreeLibrary a dll uses Boost.Log [Closed]

> Username: UMU618  
> Created at: 2024-07-27 18:03:28 UTC  
> Updated at: 2024-07-27 22:03:47 UTC  
> Closed at: 2024-07-27 22:03:46 UTC  
> Url: https://github.com/boostorg/log/issues/234  

Crash when free a plugin (a dll):  
  
1. The plugin uses Boost.Log via dll (such as `boost_log-vc144-mt-gd-x64-1_85.dll`).  
2. The plugin was dynamically loaded, and use a `severity_channel_logger_mt`.  
  
I try to speculate on possible causes:  
  
1. There is a thread_exit_callback_node in Boost.Log: `boost::this_thread::at_thread_exit([p]() { delete p; });`  
```cpp  
BOOST_LOG_API uintmax_t& get_severity_level()  
{  
    boost::log::aux::thread_specific< uintmax_t* >& tss = severity_level_holder::get();  
    uintmax_t* p = tss.get();  
    if (BOOST_UNLIKELY(!p))  
    {  
        log::aux::unique_ptr< uintmax_t > ptr(new uintmax_t(0));  
        tss.set(ptr.get());  
        p = ptr.release();  
        boost::this_thread::at_thread_exit([p]() { delete p; });  
    }  
    return *p;  
}  
```  
2. When FreeLibrary the plugin, the `boost-log.dll` (such as `boost_log-vc144-mt-gd-x64-1_85.dll`) unloaded before the `boost-thread.dll`, so when the `boost-thread.dll` unload, the `boost::run_thread_exit_callbacks()` crash.  
  
I fix the crash by load the `boost-log.dll` to increase its refcount:  
  
```cpp  
  
int main() {  
  auto dll = LoadLibraryW(L"plugin.dll");  
  if (!dll) {  
    int ec = GetLastError();  
    std::cerr << "!LoadLibraryW, #" << ec << '\n';  
    return ec;  
  }  
  auto log = LoadLibraryW(L"boost_log-vc144-mt-gd-x64-1_85.dll"); // workaround  
  int ec = Test(dll);  
  std::cout << ec << '\n';  
  FreeLibrary(dll); // this line crash if without the workaround above.  
  if (log) {  
    FreeLibrary(log);  
  }  
  return ec;  
}  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2024-07-27 22:03:46 UTC  
> Url: https://github.com/boostorg/log/issues/234#issuecomment-2254265370  

Boost.Log does not support explicitly unloaded libraries. By design, it stores references to user's code and data internally, and in some instances there is no way to remove those references. Explicitly unloading a library may result in dangling references, so this use case is intentionally not supported.
