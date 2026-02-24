# #144 - Test failures with VS 2017.4 Preview [Closed]

> Username: garyfurnish  
> Created at: 2017-08-27 23:12:13 UTC  
> Updated at: 2017-08-28 15:04:47 UTC  
> Closed at: 2017-08-28 15:04:47 UTC  
> Url: https://github.com/boostorg/fiber/issues/144  

With VS2017.4 Preview and fcb365d   
```  
--  
251701-common.mkdir bin.v2\libs\fiber\test\test_condition_variable_post_asm.test\msvc-14.1  
251702-msvc.manifest bin.v2\libs\fiber\test\test_condition_variable_any_dispatch_asm.test\msvc-14.1\release\address-model-64\link-static\threading-multi\test_condition_variable_any_dispatch_asm.exe  
251703-common.mkdir bin.v2\libs\fiber\test\test_condition_variable_post_asm.test\msvc-14.1\release  
251704-common.mkdir bin.v2\libs\fiber\test\test_condition_variable_post_asm.test\msvc-14.1\release\address-model-64  
251705-common.mkdir bin.v2\libs\fiber\test\test_condition_variable_post_asm.test\msvc-14.1\release\address-model-64\link-static  
251706-common.mkdir bin.v2\libs\fiber\test\test_condition_variable_post_asm.test\msvc-14.1\release\address-model-64\link-static\threading-multi  
251707-testing.capture-output bin.v2\libs\fiber\test\test_fiber_post_asm.test\msvc-14.1\release\address-model-64\link-static\threading-multi\test_fiber_post_asm.run  
251708-====== BEGIN OUTPUT ======  
251709-Running 14 test cases...  
251710-libs/fiber/test/test_fiber_post.cpp(204): error: in "Boost.Fiber: fiber test suite/_test_join_moveable": check value1 == 7 has failed [-1 != 7]  
251711-libs/fiber/test/test_fiber_post.cpp(220): error: in "Boost.Fiber: fiber test suite/_test_join_lambda": check value2 == "abc" has failed [ != abc]  
251712-libs/fiber/test/test_fiber_post.cpp(235): error: in "Boost.Fiber: fiber test suite/_test_join_lambda": check value2 == "abc" has failed [ != abc]  
251713-libs/fiber/test/test_fiber_post.cpp(254): error: in "Boost.Fiber: fiber test suite/_test_join_bind": check value2 == "abc" has failed [ != abc]  
251714-libs/fiber/test/test_fiber_post.cpp(270): error: in "Boost.Fiber: fiber test suite/_test_join_bind": check value2 == "abc" has failed [ != abc]  
251715-  
251716:*** 5 failures are detected in the test module "Master Test Suite"  
251717-   
251718-EXIT STATUS: 201   
251719-====== END OUTPUT ======  
251720-  
251721-      
--  
251742-    )  
251743-    exit %status%  
251744-  
251745-...failed testing.capture-output bin.v2\libs\fiber\test\test_fiber_post_asm.test\msvc-14.1\release\address-model-64\link-static\threading-multi\test_fiber_post_asm.run...  
251746-common.mkdir bin.v2\libs\fiber\test\test_condition_variable_dispatch_asm.test  
251747-common.mkdir bin.v2\libs\fiber\test\test_condition_variable_dispatch_asm.test\msvc-14.1  
251748-testing.capture-output bin.v2\libs\fiber\test\test_fiber_dispatch_asm.test\msvc-14.1\release\address-model-64\link-static\threading-multi\test_fiber_dispatch_asm.run  
251749-====== BEGIN OUTPUT ======  
251750-Running 14 test cases...  
251751-libs/fiber/test/test_fiber_dispatch.cpp(204): error: in "Boost.Fiber: fiber test suite/_test_join_moveable": check value1 == 7 has failed [-1 != 7]  
251752-libs/fiber/test/test_fiber_dispatch.cpp(220): error: in "Boost.Fiber: fiber test suite/_test_join_lambda": check value2 == "abc" has failed [ != abc]  
251753-libs/fiber/test/test_fiber_dispatch.cpp(235): error: in "Boost.Fiber: fiber test suite/_test_join_lambda": check value2 == "abc" has failed [ != abc]  
251754-libs/fiber/test/test_fiber_dispatch.cpp(254): error: in "Boost.Fiber: fiber test suite/_test_join_bind": check value2 == "abc" has failed [ != abc]  
251755-libs/fiber/test/test_fiber_dispatch.cpp(270): error: in "Boost.Fiber: fiber test suite/_test_join_bind": check value2 == "abc" has failed [ != abc]  
251756-  
251757:*** 5 failures are detected in the test module "Master Test Suite"  
251758-   
251759-EXIT STATUS: 201   
251760-====== END OUTPUT ======  
251761-  
251762-      
--  
252390-compile-c-c++ bin.v2\libs\thread\build\msvc-14.1\release\address-model-64\context-impl-winfib\link-static\threading-multi\win32\thread.obj  
252391-thread.cpp  
252392-Unknown compiler version - please run the configure tests and report the results  
252393-msvc.archive bin.v2\libs\thread\build\msvc-14.1\release\address-model-64\context-impl-winfib\link-static\threading-multi\libboost_thread-vc141-mt-1_65.lib  
252394-msvc.link bin.v2\libs\fiber\test\test_fiber_post_native.test\msvc-14.1\release\address-model-64\context-impl-winfib\link-static\threading-multi\test_fiber_post_native.exe  
252395-msvc.manifest bin.v2\libs\fiber\test\test_fiber_post_native.test\msvc-14.1\release\address-model-64\context-impl-winfib\link-static\threading-multi\test_fiber_post_native.exe  
252396-testing.capture-output bin.v2\libs\fiber\test\test_fiber_post_native.test\msvc-14.1\release\address-model-64\context-impl-winfib\link-static\threading-multi\test_fiber_post_native.run  
252397-====== BEGIN OUTPUT ======  
252398-Running 14 test cases...  
252399-libs/fiber/test/test_fiber_post.cpp(204): error: in "Boost.Fiber: fiber test suite/_test_join_moveable": check value1 == 7 has failed [-1 != 7]  
252400-libs/fiber/test/test_fiber_post.cpp(220): error: in "Boost.Fiber: fiber test suite/_test_join_lambda": check value2 == "abc" has failed [ != abc]  
252401-libs/fiber/test/test_fiber_post.cpp(235): error: in "Boost.Fiber: fiber test suite/_test_join_lambda": check value2 == "abc" has failed [ != abc]  
252402-libs/fiber/test/test_fiber_post.cpp(254): error: in "Boost.Fiber: fiber test suite/_test_join_bind": check value2 == "abc" has failed [ != abc]  
252403-libs/fiber/test/test_fiber_post.cpp(270): error: in "Boost.Fiber: fiber test suite/_test_join_bind": check value2 == "abc" has failed [ != abc]  
252404-  
252405:*** 5 failures are detected in the test module "Master Test Suite"  
252406-   
252407-EXIT STATUS: 201   
252408-====== END OUTPUT ======  
252409-  
252410-      
--  
252471-common.mkdir bin.v2\libs\fiber\test\test_cond_var_dispatch_native.test\msvc-14.1  
252472-common.mkdir bin.v2\libs\fiber\test\test_cond_var_dispatch_native.test\msvc-14.1\release  
252473-common.mkdir bin.v2\libs\fiber\test\test_cond_var_dispatch_native.test\msvc-14.1\release\address-model-64  
252474-common.mkdir bin.v2\libs\fiber\test\test_cond_var_dispatch_native.test\msvc-14.1\release\address-model-64\context-impl-winfib  
252475-common.mkdir bin.v2\libs\fiber\test\test_cond_var_dispatch_native.test\msvc-14.1\release\address-model-64\context-impl-winfib\link-static  
252476-common.mkdir bin.v2\libs\fiber\test\test_cond_var_dispatch_native.test\msvc-14.1\release\address-model-64\context-impl-winfib\link-static\threading-multi  
252477-testing.capture-output bin.v2\libs\fiber\test\test_fiber_dispatch_native.test\msvc-14.1\release\address-model-64\context-impl-winfib\link-static\threading-multi\test_fiber_dispatch_native.run  
252478-====== BEGIN OUTPUT ======  
252479-Running 14 test cases...  
252480-libs/fiber/test/test_fiber_dispatch.cpp(204): error: in "Boost.Fiber: fiber test suite/_test_join_moveable": check value1 == 7 has failed [-1 != 7]  
252481-libs/fiber/test/test_fiber_dispatch.cpp(220): error: in "Boost.Fiber: fiber test suite/_test_join_lambda": check value2 == "abc" has failed [ != abc]  
252482-libs/fiber/test/test_fiber_dispatch.cpp(235): error: in "Boost.Fiber: fiber test suite/_test_join_lambda": check value2 == "abc" has failed [ != abc]  
252483-libs/fiber/test/test_fiber_dispatch.cpp(254): error: in "Boost.Fiber: fiber test suite/_test_join_bind": check value2 == "abc" has failed [ != abc]  
252484-libs/fiber/test/test_fiber_dispatch.cpp(270): error: in "Boost.Fiber: fiber test suite/_test_join_bind": check value2 == "abc" has failed [ != abc]  
252485-  
252486:*** 5 failures are detected in the test module "Master Test Suite"  
252487-   
252488-EXIT STATUS: 201   
252489-====== END OUTPUT ======  
```

---

## Comment 1

> Username: olk  
> Created at: 2017-08-28 06:21:26 UTC  
> Url: https://github.com/boostorg/fiber/issues/144#issuecomment-325269713  

some fixes are missing in 1.65 -should be fixed in 1.65.1

---

## Comment 2

> Username: garyfurnish  
> Created at: 2017-08-28 06:39:02 UTC  
> Url: https://github.com/boostorg/fiber/issues/144#issuecomment-325272165  

This was with current master, is there a different branch I should use?

---

## Comment 3

> Username: olk  
> Created at: 2017-08-28 10:23:58 UTC  
> Url: https://github.com/boostorg/fiber/issues/144#issuecomment-325317680  

You could test branch develop or wait till boost-1.65.1is released.

---

## Comment 4

> Username: garyfurnish  
> Created at: 2017-08-28 15:04:47 UTC  
> Url: https://github.com/boostorg/fiber/issues/144#issuecomment-325379720  

Not an issue in develop.
