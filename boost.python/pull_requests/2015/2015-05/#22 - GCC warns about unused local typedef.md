# #22 GCC warns about unused local typedef [Open]

> Username: blastrock  
> Created at: 2015-05-15 14:54:11 UTC  
> Updated at: 2020-11-14 02:30:49 UTC  
> Url: https://github.com/boostorg/python/pull/22  

Same as #18 but on develop.  
  
I amended the commit to add a message on the "must be a complete type" error and I changed another typedef in make_function.hpp:56

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2015-05-15 15:04:48 UTC  
> Url: https://github.com/boostorg/python/pull/22#issuecomment-102424231  

I haven't merged the patch into my own repo yet; I'm just reviewing the code visually...  
Where are 'MORE_KEYWORDS_THAN_FUNCTION_ARGUMENTS' as well as the other new macros that substitute the removed error messages defined ?

---

## Comment 2

> Username: blastrock  
> Created_at: 2015-05-15 15:25:26 UTC  
> Url: https://github.com/boostorg/python/pull/22#issuecomment-102431779  

These are not macros, just something that will be mentioned in the error message (though I don't find it very elegant). You can try it here: http://coliru.stacked-crooked.com/a/8a7376e0edea7322  
Relevant page of doc: http://www.boost.org/doc/libs/1_58_0/libs/mpl/doc/refmanual/assert-msg.html  
  
The other option is to use BOOST_STATIC_ASSERT_MSG which takes a string, but I believe it prints the message only with c++11.  
  
Btw, I don't have a full boost worktree so I didn't run any test on this nor tried to compile it, so if you can check that before merging... :)

---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2015-05-15 15:32:14 UTC  
> Url: https://github.com/boostorg/python/pull/22#issuecomment-102433348  

On 15/05/15 11:25 AM, Philippe Daouadi wrote:  
  
> These are not macros, just something that will be mentioned in the  
> error message (though I don't find it very elegant). You can try it  
> here: http://coliru.stacked-crooked.com/a/8a7376e0edea7322  
> Relevant page of doc:  
> http://www.boost.org/doc/libs/1_58_0/libs/mpl/doc/refmanual/assert-msg.html  
  
Oh, I see. In that case we should try to preserve the exact same  
spelling (e.g., all lowercase) as the original messages.  
  
> The other option is to use BOOST_STATIC_ASSERT_MSG which takes a  
> string, but I believe it prints the message only with c++11.  
>   
> Btw, I don't have a full boost worktree so I didn't run any test on  
> this nor tried to compile it, so if you can check that before  
> merging... :)  
  
Heh, you should mention that ! I typically expect from a pull request  
that the patch was fully tested at least on one platform.  
  
OK, I'll try to do a merge to my local tree and test it, as soon as I  
get a chance.  
  
Thanks,  
        Stefan  
  
##   
  
```  
  ...ich hab' noch einen Koffer in Berlin...  
```

---

## Comment 4

> Username: blastrock  
> Created_at: 2015-05-15 15:47:40 UTC  
> Url: https://github.com/boostorg/python/pull/22#issuecomment-102442440  

The convention with BOOST_MPL_ASSERT_MSG seems to be to use capital letters (as the example shows), but I don't mind changing that.  
  
Sorry for the test, forgot to mention ^^  
Anyway, if you don't have the time until then, I'll get to try it on monday.

---

## Comment 5

> Username: stefanseefeld  
> Created_at: 2015-05-15 15:52:10 UTC  
> Url: https://github.com/boostorg/python/pull/22#issuecomment-102443970  

On 15/05/15 11:47 AM, Philippe Daouadi wrote:  
  
> The convention with BOOST_MPL_ASSERT_MSG seems to be to use capital  
> letters (as the example shows), but I don't mind changing that.  
  
There are arguments both ways: all uppercase may highlight the error  
message within a deluge of compiler-generated output. Let's see some  
actual error messages generated after the patch is applied. (There was  
some instance where the actual message changed, though, in the case of a  
missing or wrong constructor argument. That should be fixed.)  
  
> Sorry for the test, forgot to mention ^^  
> Anyway, if you don't have the time until then, I'll get to try it on  
> monday.  
  
Thanks. I doubt I'll have time before Tuesday, FWIW,  
  
```  
    Stefan  
```  
  
##   
  
```  
  ...ich hab' noch einen Koffer in Berlin...  
```

---

## Comment 6

> Username: blastrock  
> Created_at: 2015-05-20 12:54:47 UTC  
> Url: https://github.com/boostorg/python/pull/22#issuecomment-103874703  

Sorry for the delay. Well it did not compile :D  
Now fixed anyway :)  
  
Tell me if you prefer all upper or lowercase

---

## Comment 7

> Username: stefanseefeld  
> Created_at: 2015-05-20 12:58:14 UTC  
> Url: https://github.com/boostorg/python/pull/22#issuecomment-103875428  

On 20/05/15 08:54 AM, Philippe Daouadi wrote:  
  
> Sorry for the delay. Well it did not compile :D  
> Now fixed anyway :)  
>   
> Tell me if you prefer all upper or lowercase  
  
I think the first step should be to get this compiling and working, then  
we should look at the generated (error) messages and decide which is better.  
Let me know if you have a patch that is confirmed to work, and I'll try  
it in my local repo.  
  
Thanks,  
        Stefan  
  
##   
  
```  
  ...ich hab' noch einen Koffer in Berlin...  
```

---

## Comment 8

> Username: blastrock  
> Created_at: 2015-05-20 13:00:48 UTC  
> Url: https://github.com/boostorg/python/pull/22#issuecomment-103876053  

This last patch works, you can test it. I didn't check the error messages (and don't really know how to trigger them actually ^^)

---

## Comment 9

> Username: blastrock  
> Created_at: 2015-06-21 10:59:14 UTC  
> Url: https://github.com/boostorg/python/pull/22#issuecomment-113885104  

Bump?

---

## Comment 10

> Username: alkino  
> Created_at: 2015-09-04 08:52:48 UTC  
> Url: https://github.com/boostorg/python/pull/22#issuecomment-137682417  

Bump?

---
