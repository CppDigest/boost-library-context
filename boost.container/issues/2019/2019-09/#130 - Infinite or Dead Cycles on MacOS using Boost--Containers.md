# #130 - Infinite or Dead Cycles on MacOS using Boost::Containers [Closed]

> Username: ExQDev  
> Created at: 2019-09-15 21:17:05 UTC  
> Updated at: 2019-11-14 14:53:00 UTC  
> Closed at: 2019-11-14 14:52:59 UTC  
> Url: https://github.com/boostorg/container/issues/130  

Hello, I have:  
  
* MacOS Mojave (10.14.6)  
* QT with system GCC/G++(4.2.1) / CLang(Apple LLVM version 10.0.1 (clang-1001.0.46.4))  
* qmake build system  
* Boost 1.71.0  
* Homebrew (Installed boost via it)  
* Some code with boost::containers::list.  
  
App, which uses cycle, looks like:  
  
```  
for(auto cmp : components)  
            if (auto res = dynamic_cast<T*>(cmp))  
                return res;  
```  
  
Where `components` is   
```  
boost::container::list<Component*> components;  
```  
  
or even  
```  
BOOST_FOREACH(auto cmp, components){  
...  
}  
```  
  
crashes with segmentation fault error, when launched in usual mode, or debugger infinitely loops on iteration here:  
  
![image](https://user-images.githubusercontent.com/17617393/64927472-e0435c80-d813-11e9-8d89-96a608cc2094.png)  
  
Any fixes, suggestions or advices? There's no such errors on linux or windows.  
  
Report:  
```  
Process:               NukeEngine-Editor [39320]  
Path:                  /Users/USER/*/NukeEngine-Editor  
Identifier:            NukeEngine-Editor  
Version:               0  
Code Type:             X86-64 (Native)  
Parent Process:        qtcreator_process_stub [39319]  
Responsible:           NukeEngine-Editor [39320]  
User ID:               501  
  
Date/Time:             2019-09-16 00:15:00.209 +0300  
OS Version:            Mac OS X 10.14.6 (18G87)  
Report Version:        12  
Anonymous UUID:        F54BCEB5-EF43-944C-909A-3A1C6C87627E  
  
  
Time Awake Since Boot: 38000 seconds  
  
System Integrity Protection: disabled  
  
Crashed Thread:        0  Dispatch queue: com.apple.main-thread  
  
Exception Type:        EXC_BAD_ACCESS (SIGSEGV)  
Exception Codes:       KERN_INVALID_ADDRESS at 0x00000000000000f0  
Exception Note:        EXC_CORPSE_NOTIFY  
  
Termination Signal:    Segmentation fault: 11  
Termination Reason:    Namespace SIGNAL, Code 0xb  
Terminating Process:   exc handler [39320]  
  
VM Regions Near 0xf0:  
-->   
    __TEXT                 000000010a196000-000000010a1f6000 [  384K] r-x/rwx SM=COW  /Users/USER/*  
  
Thread 0 Crashed:: Dispatch queue: com.apple.main-thread  
0   NukeEngine-Editor             	0x000000010a1aa61f boost::intrusive::list_node_traits<void*>::get_next(boost::intrusive::list_node<void*>* const&) + 15 (list_node.hpp:63)  
1   NukeEngine-Editor             	0x000000010a1cffc4 boost::intrusive::list_impl<boost::intrusive::bhtraits<boost::container::dtl::list_node<NukeComponent*, void*>, boost::intrusive::list_node_traits<void*>, (boost::intrusive::link_mode_type)0, boost::intrusive::dft_tag, 1u>, unsigned long, true, void>::begin() + 52 (list.hpp:398)  
2   NukeEngine-Editor             	0x000000010a1a3029 boost::container::list<NukeComponent*, void>::begin() + 57 (list.hpp:526)  
3   NukeEngine-Editor             	0x000000010a1a3c38 Camera* GameObject::GetComponent<Camera>() + 40 (GameObject.h:36)  
4   NukeEngine-Editor             	0x000000010a1a178b EditorUI::SetUp() + 2171 (editorui.h:202)  
5   NukeEngine-Editor             	0x000000010a1a0e61 editorinit() + 17 (editorui.h:1059)  
6   NukeEngine-Editor             	0x000000010a1ea19a boost::detail::function::void_function_invoker0<void (*)(), void>::invoke(boost::detail::function::function_buffer&) + 26 (function_template.hpp:118)  
7   NukeEngine-Editor             	0x000000010a1aaf2a boost::function0<void>::operator()() const + 122 (function_template.hpp:763)  
8   NukeEngine-Editor             	0x000000010a1d2ac7 NukeBGFX::init(int, int) + 455 (nukebgfx.h:119)  
9   NukeEngine-Editor             	0x000000010a1d2d90 Camera::Init(GameObject*) + 160 (Camera.h:253)  
10  NukeEngine-Editor             	0x000000010a1d2cdc Camera::Camera(GameObject*, iRender*) + 188 (Camera.h:55)  
11  NukeEngine-Editor             	0x000000010a1a3885 Camera::Camera(GameObject*, iRender*) + 37 (Camera.h:55)  
12  NukeEngine-Editor             	0x000000010a1a36ad InitEngine() + 189 (main.cpp:112)  
13  NukeEngine-Editor             	0x000000010a1a4679 main + 41 (main.cpp:206)  
14  libdyld.dylib                 	0x00007fff646563d5 start + 1  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2019-11-14 14:52:59 UTC  
> Url: https://github.com/boostorg/container/issues/130#issuecomment-553922254  

It's not been possible to me to reproduce the bug. I'm closing it, please, if you have a reduced test case that reproduces the problem, please reopen it. Thanks for the report.
