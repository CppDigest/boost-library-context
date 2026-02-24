# #275 - 'std::filesystem::path::string': function does not take 1 arguments [Closed]

> Username: zaazbb  
> Created at: 2022-10-20 05:36:30 UTC  
> Updated at: 2022-10-20 16:20:43 UTC  
> Closed at: 2022-10-20 16:20:42 UTC  
> Url: https://github.com/boostorg/process/issues/275  

I try to replace boost.filesystem by std::filesystem, by define BOOST_PROCESS_USE_STD_FS before the includes.   
but met a error when call boost::process::system.    
  
os: windows 11 64bit  
compiler: Microsoft (R) C/C++ Optimizing Compiler Version 19.33.31630 for x86  
boost: 1.80.0  
  
codes as below  
  
``` c++  
#define BOOST_PROCESS_USE_STD_FS  
#include <boost/process.hpp>  
#include <boost/process/windows.hpp>  
  
...  
  
boost::process::system(  
                        (boost::format("rsync guest@%1%::sdcard/%2%%3%.[hg]* %4%")  
                         % ipstr % videodir % stemstr % lvdir).str(),  
                        boost::process::env["RSYNC_PASSWORD"]="password",  
                        boost::process::windows::hide));  
```  
error messages:  
  
```  
FAILED: CMakeFiles/camtool.dir/record.cpp.obj   
C:\PROGRA~1\MICROS~4\2022\ENTERP~1\VC\Tools\MSVC\1433~1.316\bin\HostX64\x86\cl.exe  /nologo /TP -DBUILD_TIME="\"2022-10-20 13:15:47\"" -DQT_CORE_LIB -DQT_GUI_LIB -DQT_NETWORK_LIB -DQT_WIDGETS_LIB -DVERSION=\"\" -DWIN32_LEAN_AND_MEAN -D_CRT_SECURE_NO_WARNINGS -D_WIN32_WINNT=0x0601 -ID:\repository\wdeyes\build-wdeyes_camtools-Qt_5_15_6_vc_x86-Debug -ID:\repository\wdeyes\wdeyes_camtools -ID:\repository\wdeyes\build-wdeyes_camtools-Qt_5_15_6_vc_x86-Debug\camtool_autogen\include -IE:\soft\boost_1_80_0_vc_x86\boost_install\include\boost-1_80 -ID:\repository\wdeyes\wdeyes_camtools\..\camsdk\src -ID:\repository\wdeyes\wdeyes_camtools\lib\tts\inc -IE:\soft\qt-everywhere-src-5.15.6-vc-x86\qtbase\include -IE:\soft\qt-everywhere-src-5.15.6-vc-x86\qtbase\include\QtWidgets -IE:\soft\qt-everywhere-src-5.15.6-vc-x86\qtbase\include\QtGui -IE:\soft\qt-everywhere-src-5.15.6-vc-x86\qtbase\include\QtANGLE -IE:\soft\qt-everywhere-src-5.15.6-vc-x86\qtbase\include\QtCore -IE:\soft\qt-everywhere-src-5.15.6-vc-x86\qtbase\.\mkspecs\win32-msvc -IE:\soft\qt-everywhere-src-5.15.6-vc-x86\qtbase\include\QtNetwork /DWIN32 /D_WINDOWS /W3 /GR /EHsc /MDd /Zi /Ob0 /Od /RTC1 /utf-8 -std:c++20 /showIncludes /FoCMakeFiles\camtool.dir\record.cpp.obj /FdCMakeFiles\camtool.dir\ /FS -c D:\repository\wdeyes\wdeyes_camtools\record.cpp  
E:\soft\boost_1_80_0_vc_x86\boost_install\include\boost-1_80\boost/process/detail/windows/basic_cmd.hpp(162): error C2660: 'std::filesystem::path::string': function does not take 1 arguments  
C:\Program Files\Microsoft Visual Studio\2022\Enterprise\VC\Tools\MSVC\14.33.31629\include\filesystem(997): note: see declaration of 'std::filesystem::path::string'  
E:\soft\boost_1_80_0_vc_x86\boost_install\include\boost-1_80\boost/process/detail/windows/basic_cmd.hpp(162): note: while compiling class template member function 'std::string boost::process::detail::windows::exe_cmd_init<Char>::get_shell(char)'  
        with  
        [  
            Char=char  
        ]  
E:\soft\boost_1_80_0_vc_x86\boost_install\include\boost-1_80\boost/process/detail/windows/basic_cmd.hpp(168): note: see reference to function template instantiation 'std::string boost::process::detail::windows::exe_cmd_init<Char>::get_shell(char)' being compiled  
        with  
        [  
            Char=char  
        ]  
E:\soft\boost_1_80_0_vc_x86\boost_install\include\boost-1_80\boost/fusion/container/vector/vector.hpp(172): note: see reference to class template instantiation 'boost::process::detail::windows::exe_cmd_init<Char>' being compiled  
        with  
        [  
            Char=char  
        ]  
E:\soft\boost_1_80_0_vc_x86\boost_install\include\boost-1_80\boost/fusion/container/vector/vector.hpp(187): note: see reference to class template instantiation 'boost::fusion::vector_detail::store<0,boost::process::detail::windows::exe_cmd_init<Char>>' being compiled  
        with  
        [  
            Char=char  
        ]  
E:\soft\boost_1_80_0_vc_x86\boost_install\include\boost-1_80\boost/fusion/tuple/tuple.hpp(42): note: see reference to class template instantiation 'boost::fusion::vector_detail::vector_data<std::integer_sequence<size_t,0,1>,boost::process::detail::windows::exe_cmd_init<Char>,boost::process::detail::windows::env_init<Char>>' being compiled  
        with  
        [  
            Char=char  
        ]  
E:\soft\boost_1_80_0_vc_x86\boost_install\include\boost-1_80\boost/process/detail/execute_impl.hpp(226): note: see reference to class template instantiation 'boost::fusion::tuple<boost::process::detail::windows::exe_cmd_init<Char>,boost::process::detail::windows::env_init<Char>>' being compiled  
        with  
        [  
            Char=char  
        ]  
E:\soft\boost_1_80_0_vc_x86\boost_install\include\boost-1_80\boost/process/detail/execute_impl.hpp(272): note: see reference to function template instantiation 'boost::process::child boost::process::detail::basic_execute_impl<req_char_type,T,boost::process::detail::env_set<char>,const boost::process::detail::windows::show_window<0>&>(T &&,boost::process::detail::env_set<char> &&,const boost::process::detail::windows::show_window<0> &)' being compiled  
        with  
        [  
            T=std::basic_string<char,std::char_traits<char>,std::allocator<char>>  
        ]  
E:\soft\boost_1_80_0_vc_x86\boost_install\include\boost-1_80\boost/process/child.hpp(35): note: see reference to function template instantiation 'boost::process::child boost::process::detail::execute_impl<_Ty,boost::process::detail::env_set<char>,const boost::process::detail::windows::show_window<0>&>(_Ty &&,boost::process::detail::env_set<char> &&,const boost::process::detail::windows::show_window<0> &)' being compiled  
        with  
        [  
            _Ty=std::basic_string<char,std::char_traits<char>,std::allocator<char>>  
        ]  
E:\soft\boost_1_80_0_vc_x86\boost_install\include\boost-1_80\boost/process/system.hpp(113): note: see reference to function template instantiation 'boost::process::child::child<_Ty,boost::process::detail::env_set<char>,const boost::process::detail::windows::show_window<0>&>(_Ty &&,boost::process::detail::env_set<char> &&,const boost::process::detail::windows::show_window<0> &)' being compiled  
        with  
        [  
            _Ty=std::basic_string<char,std::char_traits<char>,std::allocator<char>>  
        ]  
E:\soft\boost_1_80_0_vc_x86\boost_install\include\boost-1_80\boost/process/system.hpp(144): note: see reference to function template instantiation 'int boost::process::detail::system_impl<boost::asio::io_context,_Ty,boost::process::detail::env_set<char>,const boost::process::detail::windows::show_window<0>&>(std::false_type,std::false_type,_Ty &&,boost::process::detail::env_set<char> &&,const boost::process::detail::windows::show_window<0> &)' being compiled  
        with  
        [  
            _Ty=std::basic_string<char,std::char_traits<char>,std::allocator<char>>  
        ]  
D:\repository\wdeyes\wdeyes_camtools\record.cpp(69): note: see reference to function template instantiation 'int boost::process::system<std::basic_string<char,std::char_traits<char>,std::allocator<char>>,boost::process::detail::env_set<char>,const boost::process::detail::windows::show_window<0>&>(std::basic_string<char,std::char_traits<char>,std::allocator<char>> &&,boost::process::detail::env_set<char> &&,const boost::process::detail::windows::show_window<0> &)' being compiled  
ninja: build stopped: subcommand failed.  
13:31:21: The process "E:\soft\cmake-3.21.4-windows-x86_64\bin\cmake.exe" exited with code 1.  
Error while building/deploying project camtool (kit: Qt-5.15.6-vc-x86)  
When executing step "Build"  
13:31:21: Elapsed time: 00:10.  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-10-20 14:31:44 UTC  
> Url: https://github.com/boostorg/process/issues/275#issuecomment-1285652785  

Thanks for reporting, this has already been fixed in https://github.com/boostorg/process/commit/a7b65bfc44f4af3a9d584fe9347fde0944c0167a and will make 1.81.

---

## Comment 2

> Username: zaazbb  
> Created at: 2022-10-20 16:20:42 UTC  
> Url: https://github.com/boostorg/process/issues/275#issuecomment-1285829500  

ok, this issuse can be closed now.
