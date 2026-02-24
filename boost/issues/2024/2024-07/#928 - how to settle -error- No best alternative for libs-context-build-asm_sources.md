# #928 - how to settle "error: No best alternative for libs/context/build/asm_sources"? [win64] [Open]

> Username: kon0740  
> Created at: 2024-07-11 14:54:59 UTC  
> Updated at: 2024-07-11 14:54:59 UTC  
> Url: https://github.com/boostorg/boost/issues/928  

vs2022 msvc 14.32，boost1.80.0。  
when I enter:  
.\bootstrap.bat  
.\b2.exe install --toolset=msvc-14.3 --prefix="D:\Developments\thirdlibs\win64" --build-dir="D:\build-boost" link=static runtime-link=shared threading=multi address-model=64  
it always error, I don' t know how to do.
