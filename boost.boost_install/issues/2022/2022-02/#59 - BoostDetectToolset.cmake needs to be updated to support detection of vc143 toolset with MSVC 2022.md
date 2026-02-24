# #59 - BoostDetectToolset.cmake needs to be updated to support detection of vc143 toolset with MSVC 2022 [Closed]

> Username: adnsv  
> Created at: 2022-02-11 19:47:52 UTC  
> Updated at: 2022-02-12 20:41:30 UTC  
> Closed at: 2022-02-12 20:41:30 UTC  
> Url: https://github.com/boostorg/boost_install/issues/59  

When calling BoostDetectToolset.cmake using MSVC 2022 compilers, BOOST_DETECTED_TOOLSET contains an empty string.  
  
to fix: around line 92 in BoostDetectToolset.cmake: please add a couple lines as shown below:  
  
```  
if((MSVC_VERSION GREATER 1929) AND (MSVC_VERSION LESS 1940))  
    set(BOOST_DETECTED_TOOLSET "vc143")  
elseif((MSVC_VERSION GREATER 1919) AND (MSVC_VERSION LESS 1930))  
    set(BOOST_DETECTED_TOOLSET "vc142")  
else ...  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2022-02-11 21:57:18 UTC  
> Url: https://github.com/boostorg/boost_install/issues/59#issuecomment-1036662673  

Marshall, please don't transfer my issues to wrong repositories.

---

## Comment 2

> Username: mclow  
> Created at: 2022-02-11 22:10:41 UTC  
> Url: https://github.com/boostorg/boost_install/issues/59#issuecomment-1036674530  

Sorry, I thought that was where it belonged.

---

## Comment 3

> Username: pdimov  
> Created at: 2022-02-11 22:11:56 UTC  
> Url: https://github.com/boostorg/boost_install/issues/59#issuecomment-1036675683  

It was already submitted against the correct repo, boostorg/boost_install, which I maintain.
