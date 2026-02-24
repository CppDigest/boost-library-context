# #205 - CMake tests aren't built [Closed]

> Username: pdimov  
> Created at: 2025-06-08 19:56:32 UTC  
> Updated at: 2025-06-11 18:40:45 UTC  
> Closed at: 2025-06-11 18:40:45 UTC  
> Url: https://github.com/boostorg/pfr/issues/205  

Please make sure that the CMake tests are built when the target `tests` is built, per the procedure here https://github.com/boostorg/cmake?tab=readme-ov-file#testing-boost-with-cmake. Not doing so leads to CI failures, e.g. https://github.com/boostorg/boost/actions/runs/15521428592/job/43695126948
