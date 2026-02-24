# #657 - compile using msvc /clr [Open]

> Username: neuralsea-lab  
> Created at: 2020-09-21 18:48:57 UTC  
> Updated at: 2021-05-29 17:22:21 UTC  
> Url: https://github.com/boostorg/build/issues/657  

I'm using VS 2019 and msvc. I need to compile boost using /clr switch since a project that I need to reference in a C# project uses boost. Is this possible. So far I can't get it to work using msvc-14.2 e.g. b2 --toolset=msvc-14.2  
  
project-config.jam  
using msvc : : <cxxflags>"/std:c++latest" <cxxflags>"/clr" ;

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 17:21:51 UTC  
> Url: https://github.com/boostorg/build/issues/657#issuecomment-850868219  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
