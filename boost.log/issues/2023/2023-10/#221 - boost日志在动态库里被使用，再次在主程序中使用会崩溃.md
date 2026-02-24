# #221 - boost日志在动态库里被使用，再次在主程序中使用会崩溃 [Closed]

> Username: kqbi  
> Created at: 2023-10-07 05:59:53 UTC  
> Updated at: 2023-10-07 08:26:34 UTC  
> Closed at: 2023-10-07 08:26:33 UTC  
> Url: https://github.com/boostorg/log/issues/221  

场景 :因为要集成rocketmq，就编译了rocketmq-client-cpp的2.2.0版本的动态库librocketmq.so，这其中依赖boost-1.58.0并使用了boost.log的静态库，然后我的主程序使用了boost-1.83.0版本的boost.log的动态库，然后我的主程序一旦集成librocketmq.so就会在librocketmq库中调用boost.log的时候崩溃，具体崩溃在boost::log::v2s_mt_posix::sinks::text_file_backend::scan_for_files函数中，  
初步怀疑是boost.log 中有些全局调用冲突了，这个怎么解决？

---

## Comment 1

> Username: Lastique  
> Created at: 2023-10-07 08:26:33 UTC  
> Url: https://github.com/boostorg/log/issues/221#issuecomment-1751649668  

Mixing different Boost versions in the same application is not supported and likely not going to work because of ABI incompatibilities. The only solution is to use the same Boost version in all modules, including both your application and any libraries.  
  
Further, if you're using Boost.Log in multiple modules, for example a shared library and the application itself, then Boost.Log must be built as a shared library. This is necessary because Boost.Log maintains a number of singletons and will not work correctly otherwise.  
  
PS: In the future, please use English to post issues or comments.
