# #140 Initial clang_format-based reformat #137 [Merged]

> Username: nemothenoone  
> Created at: 2019-07-14 12:18:37 UTC  
> Updated at: 2020-06-20 05:42:05 UTC  
> Merged at: 2019-07-15 08:44:56 UTC  
> Closed at: 2019-07-15 08:44:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/140  

Semi-automatic reformatting according to the ```.clang_format``` introduced.  
  
This would allow us to adjust the ```.clang_format``` contents.  
  
I think this is necessary because for further development I'm going to be using semi-automatic tools, so I think do you. This is the exact result it produced according to ```.clang_format```.

---

## Comment 1

> Username: pabristow  
> Created_at: 2019-07-15 08:49:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/140#issuecomment-511321050  

FWIW I note that the & and * are now part of the variable not the type  
  
(number_backend_float_architype &o)  
(number_backend_float_architype& o)  
  
this feels C-ish to me.  
  
Is this intended?  Just checking...

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2019-07-15 11:14:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/140#issuecomment-511361452  

I confess it's not my preferred format, is there an option for this?

---

## Comment 3

> Username: nemothenoone  
> Created_at: 2019-07-15 11:17:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/140#issuecomment-511362198  

Of course. Seems like your are looking for ```PointerAlignment: Left``` option.  
  
Well, seems like we would need to unmerge this and I'ill reformat the code.

---

## Comment 4

> Username: pabristow  
> Created_at: 2019-07-15 11:24:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/140#issuecomment-511364098  

While I am bike-shedding about format :-) , I'm a strong fan of matching indenting of vertical brackets, despite the extra number of lines (though I'm not sure that John is).  
  
int foo()  
{  
  return 42;  
}  
etc  
  
And I trust we can still control some small bit of code's format with   
https://clang.llvm.org/docs/ClangFormatStyleOptions.html#disabling-formatting-on-a-piece-of-code  
?  
  
I've always been sometimes cross with fully automatic stuff (though in general agreement).

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2019-07-15 11:27:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/140#issuecomment-511364989  

I've corrected the pointer/reference issue in https://github.com/boostorg/multiprecision/pull/141.

---

## Comment 6

> Username: nemothenoone  
> Created_at: 2019-07-15 11:31:28 UTC  
> Updated_at: 2019-07-15 11:38:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/140#issuecomment-511366077  

My proposal for code style standardization purpose is not to define the standard everyone is fine with, but to define the statements set, according to which contributors could write the code in any way they like, but the clang-format call in pre-commit hook would make it all the same.  
  
For example, my personal code style is completely different from the one John is fine with, but I don't care, because at my side the code looks exactly as I want to. So I know, after the clang-format in pre-commit hook, it would look exactly like John wants in the repository.  
  
@jzmaddock I got some reformatting done across the whole library according to the fixed .clang_format.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2019-07-15 12:00:07 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/140#issuecomment-511373609  

>While I am bike-shedding about format :-) , I'm a strong fan of matching indenting of vertical brackets, despite the extra number of lines (though I'm not sure that John is).  
  
I'm fairly strongly in favour of the { aligning with the outer block and not being indented to match the content.  
  
> And I trust we can still control some small bit of code's format with  
https://clang.llvm.org/docs/ClangFormatStyleOptions.html#disabling-formatting-on-a-piece-of-code  
  
Of course.  
  
>I've always been sometimes cross with fully automatic stuff (though in general agreement).  
  
There will always be WTF moments, but by and large it does a reasonable job.  
  
> For example, my personal code style is completely different from the one John is fine with, but I don't care, because at my side the code looks exactly as I want to. So I know, after the clang-format in pre-commit hook, it would look exactly like John wants in the repository.  
  
Nod, that should work fine, and having things locally as you're used to is always easier to grok.  
  
>  I got some reformatting done across the whole library according to the fixed .clang_format.  
  
I confess I was tempted, maybe I'll just do the lot and add it to the PR.  
  
@pabristow  let me know if you spot any other SNAFU's.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2019-07-15 12:05:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/140#issuecomment-511374999  

> I confess I was tempted, maybe I'll just do the lot and add it to the PR.  
  
Done and added to the PR.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2019-07-15 17:27:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/140#issuecomment-511494781  

Reverted and tried again with new .clang-format file: https://github.com/boostorg/multiprecision/pull/142

---
