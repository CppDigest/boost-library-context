# #835 - WSVC Warning C4459: declaration of 'indices' hides global declaration [Closed]

> Username: vschoech  
> Created at: 2021-03-26 14:58:14 UTC  
> Updated at: 2022-07-08 08:24:38 UTC  
> Closed at: 2021-05-12 08:44:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/835  

Compiling with boost 1.75.0, MSVC 2019 Version 16.9.2 with compiler options `<WarningLevel>Level4</WarningLevel>` and `<LanguageStandard>stdcpplatest</LanguageStandard>`, I get the following warning:  
  
```  
14>\boost_1_75_0\boost\geometry\algorithms\detail\overlay\handle_colocations.hpp(516,1): warning C4459: declaration of 'indices' hides global declaration  
14>\boost_1_75_0\boost\multi_array\base.hpp(70,32): message : see declaration of 'boost::`anonymous-namespace'::indices'  
14>\boost_1_75_0\boost\geometry\algorithms\detail\overlay\handle_colocations.hpp(743): message : see reference to function template instantiation 'void boost::geometry::detail::overlay::discard_start_turns<Turns,Clusters>(Turns &,Clusters &)' being compiled  
14>        with  
14>        [  
14>            Turns=turn_container_type,  
14>            Clusters=cluster_type  
14>        ]  
```

---

## Comment 1

> Username: barendgehrels  
> Created at: 2021-05-12 08:44:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/835#issuecomment-839588304  

Hi @vschoech thanks for the report. Surprising that we can't use a variable just called `indices`...  
  
Anyway, that code block happens to be deleted in #845 which will be merged soon. Therefore I will close this issue.

---

## Comment 2

> Username: wanghan02  
> Created at: 2022-07-08 08:24:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/835#issuecomment-1178702993  

Thanks! The warning is gone in boost 1.79.0.
