# #48 - C2039: 'RtlCaptureStackBackTrace': is not a member of 'boost::detail::winapi' [Closed]

> Username: zlojvavan  
> Created at: 2018-04-09 15:07:37 UTC  
> Updated at: 2018-04-09 18:48:35 UTC  
> Closed at: 2018-04-09 15:38:33 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/48  

I have another problem with building project using stacktrace  
  
1>c:\project\libs\vcpkg\installed\x64-windows\include\boost\stacktrace\detail\collect_msvc.ipp(22): error C2039: 'RtlCaptureStackBackTrace': is not a member of 'boost::detail::winapi'  
1>c:\project\libs\vcpkg\installed\x64-windows\include\boost\detail\winapi\detail\deprecated_namespace.hpp(22): note: see declaration of 'boost::detail::winapi'  
1>c:\project\libs\vcpkg\installed\x64-windows\include\boost\stacktrace\detail\collect_msvc.ipp(22): error C3861: 'RtlCaptureStackBackTrace': identifier not found  
  
is this vcpkg (boost 1.66) problem?

---

## Comment 1

> Username: zlojvavan  
> Created at: 2018-04-09 15:38:33 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/48#issuecomment-379795953  

found the problem which was induced by config.hpp from boost-asio that defined _WIN32_WINNT as 0x0501 effectively hiding RtlCaptureStackBackTrace

---

## Comment 2

> Username: apolukhin  
> Created at: 2018-04-09 18:48:34 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/48#issuecomment-379854936  

Anyway, thanks for the explanation of the issue and for the initial bug report. Next time when this issue happens in user code, I'll know how to fix it.
