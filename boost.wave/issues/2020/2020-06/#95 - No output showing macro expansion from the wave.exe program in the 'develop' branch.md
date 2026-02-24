# #95 - No output showing macro expansion from the wave.exe program in the 'develop' branch [Closed]

> Username: eldiener  
> Created at: 2020-06-04 19:26:47 UTC  
> Updated at: 2020-06-05 16:28:15 UTC  
> Closed at: 2020-06-05 16:28:14 UTC  
> Url: https://github.com/boostorg/wave/issues/95  

After building the latest wave in 'develop' and using the wave.exe to see a macro expansion, the -t option produces a file with absolutely no macro exdpansio at all, while the output from the wave.exe produces a files showing the correct final output. This breaks wave.exe ability to show the steps of a macro's expansion, which I have used innumrable times in the past. Yes I am using wave.exe correctly with a:  
  
`#pragma wave trace(enable)`  
  
and a   
  
`#pragma wave trace(disable)`  
  
surrounding the macro whose expansion i want to see. Please fix this !

---

## Comment 1

> Username: jefftrull  
> Created at: 2020-06-04 19:52:42 UTC  
> Url: https://github.com/boostorg/wave/issues/95#issuecomment-639080607  

Wow! We must have broken something. Do you have a small test case? Thanks.

---

## Comment 2

> Username: jefftrull  
> Created at: 2020-06-04 20:23:07 UTC  
> Url: https://github.com/boostorg/wave/issues/95#issuecomment-639096007  

I just did a fresh build of the wave tool based on develop. The following input:  
```c++  
#define FOO(X) something X something  
#define BAR(X) FOO(X) , FOO(X)  
  
#pragma wave trace(enable)  
FOO(words)  
#pragma wave trace(disable)  
```  
produces this output:  
```  
/tmp/test_wave.cpp:5:1: FOO(words)  
  /tmp/test_wave.cpp:1:9: see macro definition: FOO(X)  
  invoked with  
  [  
    X = words  
  ]  
  [  
    something words something  
    rescanning  
    [  
      something words something  
    ]  
  ]  
#line 5 "/tmp/test_wave.cpp"  
something words something  
```  
So to me, it looks like it's working... maybe there's an issue specific to your input?

---

## Comment 3

> Username: eldiener  
> Created at: 2020-06-05 03:25:31 UTC  
> Url: https://github.com/boostorg/wave/issues/95#issuecomment-639235777  

Can you give me your wave command line ?

---

## Comment 4

> Username: jefftrull  
> Created at: 2020-06-05 04:29:41 UTC  
> Url: https://github.com/boostorg/wave/issues/95#issuecomment-639251604  

Sure! it's just:  
`wave -t - /tmp/test_wave.cpp`  
  
but if you can reproduce this problem in a small test case, it might be best just to post it and I can get started...

---

## Comment 5

> Username: eldiener  
> Created at: 2020-06-05 14:10:22 UTC  
> Url: https://github.com/boostorg/wave/issues/95#issuecomment-639519958  

Try:  
  
`wave -tsomefile /tmp/test_wave.cpp`  
  
and see if you get the output in 'somefile'.

---

## Comment 6

> Username: jefftrull  
> Created at: 2020-06-05 15:36:20 UTC  
> Url: https://github.com/boostorg/wave/issues/95#issuecomment-639569808  

Yes, they appear there as well. Can you give us a testcase?

---

## Comment 7

> Username: eldiener  
> Created at: 2020-06-05 16:28:14 UTC  
> Url: https://github.com/boostorg/wave/issues/95#issuecomment-639611896  

It was a problem with my editor and the size of the file that was produced. My editor was showing an empty file, when I tried to view the file in mu editor, even though the file was populated with output, When I gave my editor enough time to display the file, since it was very large, it would finally show the contents. I apologize for the issue report and my own error.
