# #849 - Test failures in develop with msvc-14.1 [Open]

> Username: awulkiew  
> Created at: 2021-05-16 23:31:46 UTC  
> Updated at: 2021-07-04 00:40:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/849  

This is the list of failures I get right now with msvc-14.1 (VS2017)  
```  
b2 toolset=msvc-14.1 --abbreviate-paths cxxflags="\wd4244 \wd4996" -j4 test  
```  
  
test\algorithms\detail\algorithms_approximately_equals  
```  
test/algorithms/detail/approximately_equals.cpp(75): error: in "test_main_caller( argc_ argv )": Expected: 54 Detected: 43  
test/algorithms/detail/approximately_equals.cpp(75): error: in "test_main_caller( argc_ argv )": Expected: 44 Detected: 33  
test/algorithms/detail/approximately_equals.cpp(75): error: in "test_main_caller( argc_ argv )": Expected: 34 Detected: 23  
```  
  
test\algorithms\overlay\algorithms_get_clusters  
```  
test/algorithms/overlay/get_clusters.cpp(67): error: in "test_main_caller( argc_ argv )": Case: borderx_no ctype: e expected: 0 detected: 1  
test/algorithms/overlay/get_clusters.cpp(67): error: in "test_main_caller( argc_ argv )": Case: bordery_no ctype: e expected: 0 detected: 1  
```  
  
test\algorithms\overlay\algorithms_sort_by_side  
```  
test/algorithms/overlay/sort_by_side.cpp(215): error: in "test_main_caller( argc_ argv )":   caseid=case_recursive_boxes_46_union open count: expected=[2 , 1 , 2 , 1 , 1 , 2 , 1] detected=[2 , 1 , 1 , 2 , 1 , 2 , 1]  
```  
  
----  
  
FIXED:  
  
test\algorithms\algorithms_line_interpolate  
https://github.com/boostorg/geometry/pull/854  
```  
test/algorithms/line_interpolate.cpp(60): error: in "test_main_caller( argc_ argv )": MULTIPOINT((1.4 1),(1.8 1),(2 1.2),(2 1.6),(2 2),(1.6 2),(1.2 2),(1 2.2),(1 2.6),(1 3)) != MULTIPOINT((1.4 1),(1.8 1),(2 1.2),(2 1.6),(2 2),(1.6 2),(1.2 2),(1 2.2),(1 2.6))  
test/algorithms/line_interpolate.cpp(60): error: in "test_main_caller( argc_ argv )": MULTIPOINT((1.8 1),(2 1.6),(1.6 2),(1 2.2),(1 3)) != MULTIPOINT((1.8 1),(2 1.6),(1.6 2),(1 2.2))  
test/algorithms/line_interpolate.cpp(60): error: in "test_main_caller( argc_ argv )": MULTIPOINT((1.79997 1.00002),(2 1.59985),(1.60006 2.00007),(1 2.20015),(1 3)) != MULTIPOINT((1.79997 1.00002),(2 1.59985),(1.60006 2.00007),(1 2.20015))  
```  
  
test\algorithms\overlay\algorithms_get_clusters  
https://github.com/boostorg/geometry/pull/878  
```  
test\algorithms\overlay\get_clusters.cpp(82): error C2398: Element '3': conversion from 'initializer list' to '_Ty' requires a narrowing conversion  
        with  
        [  
            _Ty=fp  
        ]  
test\algorithms\overlay\get_clusters.cpp(82): error C2398: Element '1': conversion from 'double' to 'const CoordinateType &' requires a narrowing conversion  
        with  
        [  
            CoordinateType=float  
        ]  
test\algorithms\overlay\get_clusters.cpp(83): error C2398: Element '3': conversion from 'initializer list' to '_Ty' requires a narrowing conversion  
        with  
        [  
            _Ty=fp  
        ]  
test\algorithms\overlay\get_clusters.cpp(83): error C2398: Element '1': conversion from 'double' to 'const CoordinateType &' requires a narrowing conversion  
        with  
        [  
            CoordinateType=float  
        ]  
test\algorithms\overlay\get_clusters.cpp(84): error C2398: Element '3': conversion from 'initializer list' to '_Ty' requires a narrowing conversion  
        with  
        [  
            _Ty=fp  
        ]  
test\algorithms\overlay\get_clusters.cpp(84): error C2398: Element '1': conversion from 'double' to 'const CoordinateType &' requires a narrowing conversion  
        with  
        [  
            CoordinateType=float  
        ]  
test\algorithms\overlay\get_clusters.cpp(85): error C2398: Element '3': conversion from 'initializer list' to '_Ty' requires a narrowing conversion  
        with  
        [  
            _Ty=fp  
        ]  
test\algorithms\overlay\get_clusters.cpp(85): error C2398: Element '1': conversion from 'double' to 'const CoordinateType &' requires a narrowing conversion  
        with  
        [  
            CoordinateType=float  
        ]  
```

---

## Comment 1

> Username: mloskot  
> Created at: 2021-05-17 07:47:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/849#issuecomment-842095904  

Does `variant=debug` vs `variant=release` make a difference?

---

## Comment 2

> Username: awulkiew  
> Created at: 2021-05-17 10:58:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/849#issuecomment-842227936  

> Does `variant=debug` vs `variant=release` make a difference?  
  
@mloskot No, the failures are the same in both cases. The last one is compilation error so it doesn't matter.  
  
`algorithms_line_interpolate` fails since it was implemented. AFAIU it's a numerical issue. Right @vissarion ?  
  
I created this issue to let @barendgehrels know about these failures because I think he doesn't test with msvc locally.

---

## Comment 3

> Username: vissarion  
> Created at: 2021-05-17 13:28:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/849#issuecomment-842325303  

> `algorithms_line_interpolate` fails since it was implemented. AFAIU it's a numerical issue. Right @vissarion ?  
  
right

---

## Comment 4

> Username: awulkiew  
> Created at: 2021-05-21 21:11:05 UTC  
> Updated at: 2021-05-21 21:22:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/849#issuecomment-846261978  

@vissarion I don't know why exactly but it seems that after merging https://github.com/boostorg/geometry/pull/840 `line_interpolate` test also started to fail with gcc-6.3 the same way:  
https://app.circleci.com/pipelines/github/boostorg/geometry/165/workflows/5d3af105-4f5c-49b5-a4b6-7a128f94347c/jobs/12439  
It also fails for me locally with gcc-9 as well as with clang-10.  
  
Do you have an idea why this may be happening?  
Does it make sense to alter the expected results?

---

## Comment 5

> Username: awulkiew  
> Created at: 2021-05-25 12:47:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/849#issuecomment-847839319  

The issue was automatically closed after merging a fix for one of the failures. So I reopened it and edited the issue description.
