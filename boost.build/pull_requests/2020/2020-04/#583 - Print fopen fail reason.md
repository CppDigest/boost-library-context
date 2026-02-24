# #583 Print fopen fail reason [Merged]

> Username: Kojoley  
> Created at: 2020-04-28 21:51:20 UTC  
> Updated at: 2021-10-02 21:02:10 UTC  
> Merged at: 2020-05-05 13:46:18 UTC  
> Closed at: 2020-05-05 13:46:18 UTC  
> Url: https://github.com/boostorg/build/pull/583  

Should help investigate #555

---

## Comment 1

> Username: Kojoley  
> Created_at: 2020-04-29 12:21:39 UTC  
> Url: https://github.com/boostorg/build/pull/583#issuecomment-621167258  

```  
Actual STDERR {{{  
[errno 2] failed to scan file 'dir\bin\hello1.cpp': No such file or directory  
}}}  
```  
  
I tried to understand whether this is intentional or a bug but I have no idea. Why b2 tries to find a source file in a build directory?

---

## Comment 2

> Username: mloskot  
> Created_at: 2020-04-29 12:27:33 UTC  
> Url: https://github.com/boostorg/build/pull/583#issuecomment-621170324  

Smells like [Perforce Jam](https://www.perforce.com/documentation/jam-documentation) legacy.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2020-05-01 16:31:33 UTC  
> Url: https://github.com/boostorg/build/pull/583#issuecomment-622459862  

Ok, it looks for it because it should be created by `make` rule:  
```  
t.write("dir/jamfile.jam", """\  
import common ;  
make hello1.cpp : hello.cpp : common.copy ;  
""")  
```  
I see a message that `hello1.cpp` is copied and found `hello1.obj` in build directory but there is no `hello1.cpp`, it seems to be removed by b2 and afterwards it tries to scan its content, is not it a bug?

---

## Comment 4

> Username: Kojoley  
> Created_at: 2020-05-01 17:00:35 UTC  
> Url: https://github.com/boostorg/build/pull/583#issuecomment-622471334  

It is because the test is run with `-n Don't execute the commands, only print them` flag

---

## Comment 5

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:02:09 UTC  
> Url: https://github.com/boostorg/build/pull/583#issuecomment-932820035  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
