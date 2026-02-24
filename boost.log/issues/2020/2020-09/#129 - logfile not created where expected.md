# #129 - logfile not created where expected [Closed]

> Username: barendgehrels  
> Created at: 2020-09-08 10:46:56 UTC  
> Updated at: 2020-09-08 15:52:34 UTC  
> Closed at: 2020-09-08 11:53:32 UTC  
> Url: https://github.com/boostorg/log/issues/129  

_(I also sent this to the mailing list, but this is probably a better place)._  
  
I encountered a bug, most probably, in Boost.Log  
  
It is reproduceable in the **tutorial_logging.cpp** :  
  
https://www.boost.org/doc/libs/1_74_0/libs/log/example/doc/tutorial_logging.cpp  
  
The short code should create a file named "sample.log". But it does not, instead it creates a file called "00000.log", with the expected lines logged in that file.  
  
If I compile and run exactly the same tutorial in Boost 1.70, the file sample.log is indeed created with the same content.  
  
The problem is already there in Boost 1.73  
  
What is the problem? Is there a workaround for it?  
  
Thanks, regards, Barend

---

## Comment 1

> Username: Lastique  
> Created at: 2020-09-08 11:53:32 UTC  
> Url: https://github.com/boostorg/log/issues/129#issuecomment-688817503  

Duplicates https://github.com/boostorg/log/issues/104.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2020-09-08 13:17:24 UTC  
> Url: https://github.com/boostorg/log/issues/129#issuecomment-688859425  

Thanks for the quick action. So the patch didn't make it into 1.74 yet, and I will apply it manually.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2020-09-08 14:13:11 UTC  
> Url: https://github.com/boostorg/log/issues/129#issuecomment-688904321  

The extra constructor, referred to in https://github.com/boostorg/parameter/pull/99 is already there (in 1.73 and in 1.74).   
It doesn't help the problem. The reproduction scenario you provided (https://github.com/boostorg/parameter/issues/97) still doesn't output the sample.log  
  
Or I'm also lost in the multiple commits and reversions in the different PR's.  
  
Anyway, if I compile your program on 1.74 and 1.73, it doesn't output the sample.log name. On 1.70 it still works. I checked the `tagged_argument.hpp` file in the different versions and they **have** that extra constructor.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2020-09-08 14:14:33 UTC  
> Url: https://github.com/boostorg/log/issues/129#issuecomment-688905246  

The workaround provided in the issue it duplicates works for me, in the creation of the logfile.  
But how can I be sure nothing in our code uses the boost.parameters in another way... but that is not a Boost.Log problem.

---

## Comment 5

> Username: Lastique  
> Created at: 2020-09-08 15:06:26 UTC  
> Url: https://github.com/boostorg/log/issues/129#issuecomment-688940795  

On 2020-09-08 17:13, Barend Gehrels wrote:  
> The extra constructor, referred to in boostorg/parameter#99   
> <https://github.com/boostorg/parameter/pull/99> is already there (in   
> 1.73 and in 1.74).  
  
That PR does not add a constructor, but a subscript operator, and it wasn't released in any Boost release yet.  
  
You need to cherry-pick commit https://github.com/boostorg/parameter/commit/201a7e20d0a14c41f234ea4fbc25f19de8dae213.

---

## Comment 6

> Username: barendgehrels  
> Created at: 2020-09-08 15:52:34 UTC  
> Url: https://github.com/boostorg/log/issues/129#issuecomment-688970746  

Thanks - I will continue with it on Thursday.
