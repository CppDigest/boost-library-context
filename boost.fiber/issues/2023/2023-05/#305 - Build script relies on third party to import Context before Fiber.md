# #305 - Build script relies on third party to import Context before Fiber [Open]

> Username: Kojoley  
> Created at: 2023-05-01 19:43:56 UTC  
> Updated at: 2023-05-01 19:43:56 UTC  
> Url: https://github.com/boostorg/fiber/issues/305  

Fiber build script uses `<segmented-stacks>` feature https://github.com/boostorg/fiber/blob/2cb72f5dcefdeffbb36636234e6ccb36282f8ae3/build/Jamfile.v2#L23-L26 which is defined in Context library build script. It works currently by pure luck that `boost-cpp.jam` (main Boost build script) was unintentionally consistently importing Context before Fiber.  
  
Possible solutions:  
1. If `-fsplit-stack -DBOOST_USE_SEGMENTED_STACKS` is not needed by Fiber itself -- we can just delete these lines and issue will be resolved.  
2. If `-fsplit-stack -DBOOST_USE_SEGMENTED_STACKS` is needed by Fiber -- we can put there in Context usage requirements and Fiber will get them automatically by using `<library>/boost/context//boost_context`.
