# #45 - Incomple stacktrace with WinDbg [Closed]

> Username: githubuser0xFFFF  
> Created at: 2018-02-07 10:09:18 UTC  
> Updated at: 2018-10-30 16:52:55 UTC  
> Closed at: 2018-10-30 16:52:55 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/45  

I use BOOST_BACKTRACE_USE_WINDBG here for printing stacktraces in my MinGW compiled application. If I print the stacktrace in the debug version of my application, I will get a complete stacktrace. If I print the stacktrace in the release version (-fno-omit-frame-pointer and debug symbols not stripped), some levels are missing.   
  
This is the stack of the **release version** that is shown in the QtCreator debugger:  
  
- 1  Usl::Diag::newHandler                                                diag.cpp              151 0x6a51061e   
- 2  libstdc++-6!_Znwj                                                                              0x6fef99ae   
- 3  __gnu_cxx::new_allocator<double>::allocate                           new_allocator.h       104 0x308877e    
- 4  std::allocator_traits<std::allocator<double>>::allocate              alloc_traits.h        360 0x308877e    
- 5  std::_Vector_base<double, std::allocator<double>>::_M_allocate       stl_vector.h          170 0x308877e    
- 6  std::_Vector_base<double, std::allocator<double>>::_M_create_storage stl_vector.h          185 0x308877e    
- 7  std::_Vector_base<double, std::allocator<double>>::_Vector_base      stl_vector.h          136 0x308877e    
- 8  std::vector<double>::vector                                          stl_vector.h          291 0x308877e    
- 9  QtLabb::Core::CDeviceManagerPrivate::onConnect                       DeviceManager.cpp     219 0x308877e    
- 10 QtLabb::Core::CDeviceManager::qt_static_metacall                     moc_DeviceManager.cpp 109 0x308d13b    
- 11 ZN11QMetaObject8activateEP7QObjectiiPPv                                                        0x68c7b2f2   
- 12 ZN7QAction8activateENS_11ActionEventE                                                          0x2a63e01    
  
This is the stack trace that is printed by boost stacktrace for the **release version**:  
  
- 0# boost::stacktrace::basic_stacktrace<std::allocator<boost::stacktrace::frame> >::init(unsigned int, unsigned int) in usl  
-  1# Usl::Diag::newHandler() in usl  
-  2# operator new(unsigned int) in libstdc___6  
-  3# QtLabb::Core::CDeviceManager::qt_static_metacall(QObject*, QMetaObject::Call, int, void**) in qtlabb_core  
-  4# QMetaObject::activate(QObject*, int, int, void**) in Qt5Core  
-  5# QAction::activate(QAction::ActionEvent) in Qt5Widgets  
  
This is the stack trace that is printed by boost stacktrace for the **debug version**:  
  
- 0# boost::stacktrace::basic_stacktrace<std::allocator<boost::stacktrace::frame> >::init(unsigned int, unsigned int) in usl_d  
-  1# Usl::Diag::newHandler() in usl_d  
-  2# operator new(unsigned int) in libstdc___6  
-  3# std::allocator_traits<std::allocator<double> >::allocate(std::allocator<double>&, unsigned int) in qtlabb_cored  
-  4# std::_Vector_base<double, std::allocator<double> >::_M_allocate(unsigned int) in qtlabb_cored  
-  5# std::_Vector_base<double, std::allocator<double> >::_M_create_storage(unsigned int) in qtlabb_cored  
-  6# std::_Vector_base<double, std::allocator<double> >::_Vector_base(unsigned int, std::allocator<double> const&) in qtlabb_cored  
-  7# std::vector<double, std::allocator<double> >::vector(unsigned int, double const&, std::allocator<double> const&) in qtlabb_cored  
-  8# QtLabb::Core::CDeviceManagerPrivate::onConnect() in qtlabb_cored  
-  9# QtLabb::Core::CDeviceManager::qt_static_metacall(QObject*, QMetaObject::Call, int, void**) in qtlabb_cored  
- 10# QMetaObject::activate(QObject*, int, int, void**) in Qt5Cored  
- 11# QMetaObject::activate(QObject*, QMetaObject const*, int, void**) in Qt5Cored  
  
So the QtCreator debugger can extract a more detailed stacktrace from the release build than the boost stacktrace library. Is this normal or is this an issue?

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-10-30 16:52:55 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/45#issuecomment-434382268  

Yes, this seems to be OK. Looks like the debugger in QtCreator has some logic to add the missing levels.  
  
It executes the binary, so it has more control over the binary and less restrictions than the "stack trace from itself" approach.
