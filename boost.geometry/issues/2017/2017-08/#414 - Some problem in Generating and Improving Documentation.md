# #414 - Some problem in Generating and Improving Documentation [Closed]

> Username: Rylynnn  
> Created at: 2017-08-09 12:24:31 UTC  
> Updated at: 2018-07-29 19:31:53 UTC  
> Closed at: 2017-08-18 23:42:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/414  

Here is a little problem in Generating and Improving Documentation.  
  
The path when I followed the step compiling doxygen_xml2qbk tool, the path of the following call does not  
work.  
```  
./b2 libs/geometry/src/docutils/tools/doxygen_xml2qbk  
```  
So, I searched the folder and found the right path is:  
```  
./b2 libs/geometry/doc/src/docutils/tools/doxygen_xml2qbk  
```  
  
Could you please check this problem, or this problem happened only on my linux?

---

## Comment 1

> Username: Rylynnn  
> Created at: 2017-08-09 12:24:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/414#issuecomment-321239865  

@awulkiew

---

## Comment 2

> Username: mloskot  
> Created at: 2017-08-09 13:57:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/414#issuecomment-321263009  

@Rylynnn   
>  I followed the step compiling doxygen_xml2qbk tool  
  
Do you refer to the [doc/readme.txt](https://github.com/boostorg/geometry/blob/develop/doc/readme.txt) instructions? where they say this?  
  
> 3) run b2 in src/docutils/tools/doxygen_xml2qbk to build doxygen_xml2qbk executable

---

## Comment 3

> Username: awulkiew  
> Created at: 2017-08-09 15:06:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/414#issuecomment-321283927  

@Rylynnn ok thanks I'll fix it.  
  
@mloskot no, about Wiki: https://github.com/boostorg/geometry/wiki/Generating-and-Improving-Documentation

---

## Comment 4

> Username: awulkiew  
> Created at: 2017-08-09 15:12:12 UTC  
> Updated at: 2017-08-09 15:17:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/414#issuecomment-321285704  

@Rylynnn also section **Building documentation** was outdated because instead of running `make_qbk.py` one should now run `b2` (`../../../b2`) from `doc` directory.  
  
Were you able to build the docs?

---

## Comment 5

> Username: Rylynnn  
> Created at: 2017-08-10 05:19:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/414#issuecomment-321453410  

@awulkiew no, i won't able to build the docs yesterday, and I did not find the solution, so I did not put this bug to you.  
But I follow the new step now, and it works, but in my local terminal, it failed updating 1 target, the whole report is [here](https://gist.github.com/Rylynnn/d58bf3221eb72e6631d128c7dabdc534).

---

## Comment 6

> Username: awulkiew  
> Created at: 2017-08-10 12:08:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/414#issuecomment-321533045  

> Make sure that you can call all of the above programs from any directory, including doxygen_xml2qbk and b2. If needed add Boost root directory and BOOST_ROOT/dist/bin to PATH.  
  
Have you done that?

---

## Comment 7

> Username: Rylynnn  
> Created at: 2017-08-16 19:47:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/414#issuecomment-322878985  

It works! :)Thank a lot!

---

## Comment 8

> Username: awulkiew  
> Created at: 2017-08-18 23:42:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/414#issuecomment-323483754  

I'm glad it works. Closing.
