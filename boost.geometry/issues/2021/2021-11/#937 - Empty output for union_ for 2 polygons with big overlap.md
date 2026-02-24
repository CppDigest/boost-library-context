# #937 - Empty output for union_ for 2 polygons with big overlap [Closed]

> Username: MortenTG  
> Created at: 2021-11-16 14:21:06 UTC  
> Updated at: 2021-11-17 17:56:31 UTC  
> Closed at: 2021-11-17 15:13:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/937  

When union_ is used on these 2 polygons the resulting output is empty. Case can be seen by compiling an running the cpp file in the zip file.  
  
How to compile "g++ bug.cpp -isystem boost directory"  
  
Checked versions:  
boost-1.73  
boost-1.77  
  
[bug.zip](https://github.com/boostorg/geometry/files/7546976/bug.zip)  
  
![boost_union_polygons](https://user-images.githubusercontent.com/94462727/142001346-4db924f4-45ec-4dde-924e-238d83c18391.png)

---

## Comment 1

> Username: mloskot  
> Created at: 2021-11-16 17:35:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/937#issuecomment-970505570  

Add this at the top of your `bug.cpp` file and you will get the non-empty result  
  
```cpp  
#define BOOST_GEOMETRY_NO_ROBUSTNESS  
```

---

## Comment 2

> Username: MortenTG  
> Created at: 2021-11-17 09:20:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/937#issuecomment-971388369  

Hi  
  
Thanks for the info about this setting. Currently I don't know how it will impact the many other cases in the code the test case originated from, so will need to test that.  
  
Is there by chance some documentation/description on what this setting does, because from reading some other bug report and headers, I can see it probably impacts some integer scaling and controls the setting BOOST_GEOMETRY_USE_RESCALING.  
  
Best regards Morten

---

## Comment 3

> Username: mloskot  
> Created at: 2021-11-17 09:50:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/937#issuecomment-971411987  

> I don't know how it will impact the many other cases in the code  
  
The define makes tells the library to not apply rescaling of float-point coordinates to integer grid.  
  
https://www.boost.org/doc/libs/1_77_0/boost/geometry/core/config.hpp says this about the rescaling:  
  
> In future versions of Boost.Geometry, **it will be turned off** by default

---

## Comment 4

> Username: MortenTG  
> Created at: 2021-11-17 15:13:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/937#issuecomment-971676722  

Ran the test we have with #define BOOST_GEOMETRY_NO_ROBUSTNESS  
  
There was a couple visual changes, but none of concern that would suggest incorrect results, so seems like this is a good option. Considering it will become default at some point that is a good sign, so good work with the progress. Thanks for the feedback.

---

## Comment 5

> Username: mloskot  
> Created at: 2021-11-17 15:52:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/937#issuecomment-971712792  

@awulkiew Does such issue qualify to be converted to discussion?

---

## Comment 6

> Username: barendgehrels  
> Created at: 2021-11-17 17:47:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/937#issuecomment-971813735  

Thanks @mloskot for handling this issue!  
I'm still busy with changing it the default. Version 1.78 will it not be (obviously), but hopefully 1.79

---

## Comment 7

> Username: awulkiew  
> Created at: 2021-11-17 17:56:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/937#issuecomment-971820579  

@mloskot I don't know. To be honest I'm leaning away from a question and more towards a bug which was fixed by @barendgehrels already but with the fix disabled by default so technically still present.
