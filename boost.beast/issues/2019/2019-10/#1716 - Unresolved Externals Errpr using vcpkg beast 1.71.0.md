# #1716 - Unresolved Externals Errpr using vcpkg beast  1.71.0 [Closed]

> Username: Chris-SG  
> Created at: 2019-10-03 13:28:58 UTC  
> Updated at: 2019-10-04 00:41:51 UTC  
> Closed at: 2019-10-04 00:40:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1716  

Currently working with vcpkg supplied boost. BOOST_BEAST_VERSION is 266.  
  
I am currently writing a small test program based off the code located at https://www.boost.org/doc/libs/1_71_0/libs/beast/example/http/server/fast/http_server_fast.cpp  
  
The line in particular causing an error is the process_request declaration:  
        void `process_request(boost::beast::http::request<boost::beast::http::string_body,boost::beast::http::basic_fields<fields_alloc<char>>> const& req);`  
  
This is using cmake with Ninja however I have tried with VS2019 too.  
  
Error with Ninja:  
```  
[2/2] Linking CXX executable server.exe  
FAILED: server.exe  
cmd.exe /C "cd . && "C:\Program Files\CMake\bin\cmake.exe" -E vs_link_exe --intdir=CMakeFiles\server.dir --rc=C:\PROGRA~2\WI3CF2~1\10\bin\100183~1.0\x64\rc.exe --mt=C:\PROGRA~2\WI3CF2~1\10\bin\100183~1.0\x64\mt.exe --manifests  -- C:\PROGRA~2\MICROS~1\2019\COMMUN~1\VC\Tools\MSVC\1422~1.279\bin\Hostx64\x64\link.exe /nologo CMakeFiles\server.dir\server.cpp.obj CMakeFiles\server.dir\worker.cpp.obj  /out:server.exe /implib:server.lib /pdb:server.pdb /version:0.0  /machine:x64 /INCREMENTAL:NO /subsystem:console  D:\Git\vcpkg\installed\x64-windows\lib\boost_system-vc140-mt.lib kernel32.lib user32.lib gdi32.lib winspool.lib shell32.lib ole32.lib oleaut32.lib uuid.lib comdlg32.lib advapi32.lib && cmd.exe /C "cd /D D:\Git\server\build && powershell -noprofile -executionpolicy Bypass -file D:/Git/vcpkg/scripts/buildsystems/msbuild/applocal.ps1 -targetBinary D:/Git/server/build/server.exe -installedDir D:/Git/vcpkg/installed/x64-windows/bin -OutVariable out""  
LINK: command "C:\PROGRA~2\MICROS~1\2019\COMMUN~1\VC\Tools\MSVC\1422~1.279\bin\Hostx64\x64\link.exe /nologo CMakeFiles\server.dir\server.cpp.obj CMakeFiles\server.dir\worker.cpp.obj /out:server.exe /implib:server.lib /pdb:server.pdb /version:0.0 /machine:x64 /INCREMENTAL:NO /subsystem:console D:\Git\vcpkg\installed\x64-windows\lib\boost_system-vc140-mt.lib kernel32.lib user32.lib gdi32.lib winspool.lib shell32.lib ole32.lib oleaut32.lib uuid.lib comdlg32.lib advapi32.lib /MANIFEST /MANIFESTFILE:server.exe.manifest" failed (exit code 1120) with the following output:  
worker.cpp.obj : error LNK2019: unresolved external symbol "private: void __cdecl worker::Impl::process_request(class boost::beast::http::message<1,struct boost::beast::http::basic_string_body<char,struct std::char_traits<char>,class std::allocator<char> >,class boost::beast::http::basic_fields<struct fields_alloc<char> > > const &)" (?process_request@Impl@worker@@AEAAXAEBV?$message@$00U?$basic_string_body@DU?$char_traits@D@std@@V?$allocator@D@2@@http@beast@boost@@V?$basic_fields@U?$fields_alloc@D@@@234@@http@beast@boost@@@Z) referenced in function "public: void __cdecl boost::beast::async_base<class <lambda_b655d4cfaab15fe0dbfc7707e0b5ba15>,class boost::asio::executor,class std::allocator<void> >::complete_now<class boost::system::error_code &,unsigned __int64 &>(class boost::system::error_code &,unsigned __int64 &)" (??$complete_now@AEAVerror_code@system@boost@@AEA_K@?$async_base@V<lambda_b655d4cfaab15fe0dbfc7707e0b5ba15>@@Vexecutor@asio@boost@@V?$allocator@X@std@@@beast@boost@@QEAAXAEAVerror_code@system@2@AEA_K@Z)  
server.exe : fatal error LNK1120: 1 unresolved externals  
```  
  
Error with msvc:  
```  
1>worker.obj : error LNK2019: unresolved external symbol "private: void __cdecl worker::Impl::process_request(class boost::beast::http::message<1,struct boost::beast::http::basic_string_body<char,struct std::char_traits<char>,class std::allocator<char> >,class boost::beast::http::basic_fields<struct fields_alloc<char> > > const &)" (?process_request@Impl@worker@@AEAAXAEBV?$message@$00U?$basic_string_body@DU?$char_traits@D@std@@V?$allocator@D@2@@http@beast@boost@@V?$basic_fields@U?$fields_alloc@D@@@234@@http@beast@boost@@@Z) referenced in function "public: void __cdecl <lambda_d62a11699669f5af77d1d512eb9df72f>::operator()(class boost::system::error_code,unsigned __int64)const " (??R<lambda_d62a11699669f5af77d1d512eb9df72f>@@QEBAXVerror_code@system@boost@@_K@Z)  
1>D:\Git\server\build\Debug\server.exe : fatal error LNK1120: 1 unresolved externals  
```  
Upon removing the function declaration and definition, it compiles fine. Please let me know if I am missing any information.  
  
My CMakeLists:  
```  
cmake_minimum_required(VERSION 3.15)  
project(server_proj)  
  
add_executable(server server.cpp worker.hpp worker.cpp fields_alloc.hpp)  
  
  
find_package(Boost 1.71 COMPONENTS system REQUIRED)  
  
target_include_directories(server PUBLIC ${Boost_INCLUDE_DIRS})  
  
target_link_libraries(server Boost::system)  
```  
  
Function Body:  
```  
/**  
 *   
 */  
void process_request(boost::beast::http::request<boost::beast::http::string_body, boost::beast::http::basic_fields<fields_alloc<char>>> const& req)  
{  
}  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-10-03 13:30:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1716#issuecomment-537946357  

No idea.. `worker::Impl::process_request` looks like one of your functions

---

## Comment 2

> Username: Chris-SG  
> Created at: 2019-10-03 13:33:52 UTC  
> Updated at: 2019-10-03 13:34:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1716#issuecomment-537947849  

That is the function which causes the error, however it is a completely blank function. I believe it may be a result of the parameters but I cannot work out why.  
  
I added my CMakeLists to the main post.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-10-03 13:38:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1716#issuecomment-537949577  

Can you reproduce the problem here? https://godbolt.org

---

## Comment 4

> Username: Chris-SG  
> Created at: 2019-10-04 00:40:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1716#issuecomment-538182409  

Resolved, somehow didn't realise I had my function definition wrong. Must have been too tired, sorry for the troubles!

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-10-04 00:41:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1716#issuecomment-538182578  

Happens to everyone sooner or later
