# #189 Fix compilation error in visual studio Version 17.14.24 [Closed]

> Username: misganabelachew  
> Created at: 2026-01-20 06:19:29 UTC  
> Updated at: 2026-01-20 06:27:12 UTC  
> Closed at: 2026-01-20 06:27:12 UTC  
> Url: https://github.com/boostorg/iostreams/pull/189  

The newer MSVC compiler removed the non-standard seekpos() member function from std::fpos, which current Boost  positioning.hpp relies on.  
  
Environment info:  
Microsoft Visual Studio Professional 2022  
Version 17.14.24  
VisualStudio.17.Release/17.14.24+36908.2  
Installed Version: Professional  
Visual C++ 2022   00476-80000-00000-AA410  
Microsoft Visual C++ 2022

---
