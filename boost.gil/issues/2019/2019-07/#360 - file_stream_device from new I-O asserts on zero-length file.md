# #360 - file_stream_device from new I/O asserts on zero-length file [Closed]

> Username: kkumar45  
> Created at: 2019-07-30 11:31:45 UTC  
> Updated at: 2019-08-09 08:06:08 UTC  
> Closed at: 2019-08-09 08:05:57 UTC  
> Url: https://github.com/boostorg/gil/issues/360  

There is a possibility of zero length file. Such test case results in false assert.  
  
False assert from   
```  
file : boost/gil/io/device.hpp  
BOOST_ASSERT(num_elements > 0 );  
```

---

## Comment 1

> Username: mloskot  
> Created at: 2019-07-30 11:42:49 UTC  
> Updated at: 2019-07-30 11:45:26 UTC  
> Url: https://github.com/boostorg/gil/issues/360#issuecomment-516383468  

> There is a possibility of zero length file  
  
When, in what circumstances?   
With what purpose an empty file is used?  
Using what file, in what format?  
  
Please spare some time and give us more detail on what you're doing, than just a sloppy report.

---

## Comment 2

> Username: xjhouska  
> Created at: 2019-07-30 13:07:58 UTC  
> Url: https://github.com/boostorg/gil/issues/360#issuecomment-516410863  

Hi Mateusz,  
  
this was actually me who has reported the issue to Kunal. The zero-length file is always an error condition and has no practical purpose, but we don't want the code to assert on it. Instead, we'd prefer the caller to receive zero bytes and leave it up to the caller to handle that error condition.  
  
Our specific use case is a software that needs to read JPEG files. There can be multiple image sources for a single image and if we encounter an invalid (non-existing, corrupted, whatever) JPEG file, we'd like to be able to ignore that and proceed to the next file in the file list, in a hope that the next file in the list will be OK. This includes skipping a zero-length file because a zero-length file is quite a frequent outcome of a failed download. We have this implemented with Boost 1.65.1 and it works without problems. This assert was added somewhere between 1.65.1 and 1.70 and it breaks our application here.  
  
We have a test suite on reading various JPEG files and, in addition to valid JPEG files, we also have negative test cases with files that are not valid JPEG. This includes non-empty non-JPEG contents and also a zero-length file. That's how we discovered the issue: when upgrading from Boost 1.65.1 (which does not have the issue) to Boost 1.70, the test suite did not provide the expected error message but crashed the application , reporting the assert on the zero-length file.  
  
Hope this makes sense. Please let me know if you need more information.  
  
Thanks, Jan

---

## Comment 3

> Username: mloskot  
> Created at: 2019-07-30 21:50:18 UTC  
> Updated at: 2019-07-30 21:51:42 UTC  
> Url: https://github.com/boostorg/gil/issues/360#issuecomment-516609578  

Jan, thanks for the details explanation. It is helpful indeed.  
  
> This assert was added somewhere between 1.65.1 and 1.70 and it breaks our application here.  
  
That is not quite the whole story what happened.  
  
GIL in Boost 1.68, https://www.boost.org/users/history/version_1_68_0.html, received new entirely rewritten I/O extension (aka IOv2 which was [reviewed and accepted](https://lists.boost.org/boost-announce/2011/01/0281.php) long time). The new implementation makes use of assertions.  
  
> This includes skipping a zero-length file because a zero-length file is quite a frequent outcome  
> of a failed download. We have this implemented with Boost 1.65.1 and it works without problems.  
> This assert (...) breaks our application here.  
  
All assertions in current GIL are based on [Boost.Assert](https://www.boost.org/doc/libs/1_70_0/libs/assert/doc/html/assert.html) utility and use `BOOST_ASSERT` macro. Similarly to C macro `assert`, it is no-op if `NDEBUG` is defined.   
  
If you want to avoid this assertion, add define [-DBOOST_DISABLE_ASSERTS=1](https://www.boost.org/doc/libs/1_70_0/libs/assert/doc/html/assert.html#boost_assert) to build configuration of your tests/applications in order to disable `BOOST_ASSERT` regardless of target build.   
  
------  
  
I'm afraid removing that or any other assertion to fulfil a project-specific requirements, especially related to its testing environment, is not a reasonable approach.  
  
AFAIU, you are running your tests from debug builds. If that is the case, then your tests are prone to crashes due to any `assert` in any third-party library you use.  
  
One may argue the `BOOST_ASSERT(num_elements > 0 );` should be replaced with  `throw std::runtime_error`  or similar to make the error condition even stricter regardless of `NDEBUG`. But, not removing it entirely though. Especially, it can be easily controlled in some sophisticated use cases (i.e.  `BOOST_DISABLE_ASSERTS`).  
  
/cc @stefanseefeld

---

## Comment 4

> Username: mloskot  
> Created at: 2019-07-30 21:55:08 UTC  
> Url: https://github.com/boostorg/gil/issues/360#issuecomment-516610990  

Renaming the subject as the assertions may be annoying, but certainly not `False assert`.

---

## Comment 5

> Username: xjhouska  
> Created at: 2019-08-02 16:19:55 UTC  
> Url: https://github.com/boostorg/gil/issues/360#issuecomment-517761463  

Hi Mateusz,  
  
my understanding of asserts is that they are designed to check for assumptions that, if failed, indicate programming errors. In our case, e.g. checking if the file size is non-negative would be a good use of assert. Negative file size can never happen and if it happens, it indicates that someone has computed or passed the file information incorrectly. In case of a debugged, tested program, such an assert can never fail.  
  
But this is not the case for zero file size. Zero-sized files do exist, can be passed as inputs, and functions should be able to handle them gracefully, not to fail an assert on them. Handling asserts, there are two possibilities - either they are enabled, and then the application aborts with no chance to recover. Or they are disabled, and then something what was considered a fatal condition during debugging goes unnoticed to the caller, where it does - who knows what, because it was never tested during debugging. Neither of these two cases is acceptable in a released application I think.  
  
As far as solutions I'd prefer, there are several possibilities. The best is simply to remove the assert. The function is allowed to return a smaller number of bytes than requested anyway, so why not zero? Callers are apparently able to handle an incomplete data read, so why they shouldn't be able to handle zero data read (which is just "even more incomplete")? If this is not an option for some reason then throwing an exception would be the second best option.  
  
Then, I would like to comment on your statement "I'm afraid removing that or any other assertion to fulfil a project-specific requirements, especially related to its testing environment, is not a reasonable approach.". This is a misunderstanding - this is not a testing environment issue, and we do not want to remove the assert just to fulfill testing environment requirements. We want to remove the assert so that our application does not crash on our users when they supply a zero-sized file by mistake, and the testing environment just helped us to discover that this would happen after we have upgraded Boost.  
  
Finally, thinking of it further, I believe that one should never fail an assert on a runtime condition, like e.g. zero-sized file, file reading error, disk full, or such. These do happen and should be properly handled and not crashed upon (enabled asserts) or ignored (disabled asserts). If the new I/O extension uses asserts for reporting this kind of errors, I'd consider it incorrectly designed, and I wonder how the reviewers could have missed that. A bold statement, I know, but here it goes.  
  
Thanks, Jan

---

## Comment 6

> Username: mloskot  
> Created at: 2019-08-02 17:00:35 UTC  
> Updated at: 2019-08-02 17:02:58 UTC  
> Url: https://github.com/boostorg/gil/issues/360#issuecomment-517774587  

>  If the new I/O extension uses asserts for reporting this kind of errors, , I'd consider it incorrectly designed.  
  
No, that is not the case!  
  
I think the IO authors just used the assertions as a debugging facility, _not error reporting_.  
Likely, they assumed, _correctly_, that `BOOST_ASSERT` is no-opt in end-user application, because end-users receive optimised binaries compiled with `NDEBUG`, so those assertions do not exists in end-user code, not causing any crashes. There is nothing wrong with spreading assertions across codebase even for run-time checks, as inline debugger breakpoints, because those will be gone before code is deployed to users. Assertions fall into category of comments, a subjective matter left at discretion of authors, and that is not a matter of reviewers' opinion, I believe.  
What reviewers could have pointed out though was lack of (enough) `throws`.  
  
So, ...  
  
> We want to remove the assert so that our application does not crash on our users   
  
...this should never be the case. Unless, you ship debug built binaries (with `NDEBUG` disabled) to your end-users.  
  
>  We want to remove the assert so that our application does not crash  
  
Removing the assertions without further improvements to the code (i.e. throwing exceptions) equals disabling the assertions.  
This is already the case for `NDEBUG` builds. For debug builds, I suggested to use `-DBOOST_DISABLE_ASSERTS=1` to achieve the same effect.  
That was a considerate suggestion and I believe there was no misunderstanding.  
  
------  
**Disclaimer**: _I'm not the author of the IO and I'm not in the position of IO authors advocate, so I'm not going to keep exchanging arguments about their choices._  
  
------  
I, however, do understand your points and I do agree with you the IO implementation calls for improvements in the area of run-time error handling and reporting.  
  
What I can suggest you guys is to consider submitting a pull request with the necessary improvements.  
That is, replacing the assertions with better run-time errors reporting (e.g. exceptions).  
Just removing the assertions is not a solution as I explained earlier.  
  
/cc @chhenning, @stefanseefeld

---

## Comment 7

> Username: mloskot  
> Created at: 2019-08-02 17:12:23 UTC  
> Url: https://github.com/boostorg/gil/issues/360#issuecomment-517778372  

Meanwhile, I forwarded the discussion to the mailing list with hope to make other developers aware (incl. IO authors): [[GH#360] New IO receives complains about use of assertions](https://lists.boost.org/boost-gil/2019/08/0288.php)

---

## Comment 8

> Username: xjhouska  
> Created at: 2019-08-05 09:01:06 UTC  
> Url: https://github.com/boostorg/gil/issues/360#issuecomment-518149459  

Hi Mateusz,  
  
unfortunately, I'm not convinced. If the assertions are a debugging facility, what kind of **bug** is it to supply a zero-sized file as an argument? IMHO this is a runtime condition that can happen easily and the library should be able to handle it gracefully. I stand by my statement that one should never fail an assertion on a runtime condition.  
  
I understand that you, not being the author, don't want to continue the discussion anymore. Thank you for forwarding to other developers, including the authors. Hopefully someone from them will be available to make further comments.  
  
Thanks, Jan

---

## Comment 9

> Username: mloskot  
> Created at: 2019-08-05 09:21:46 UTC  
> Updated at: 2019-08-05 09:22:28 UTC  
> Url: https://github.com/boostorg/gil/issues/360#issuecomment-518156563  

@xjhouska   
> If the assertions are a debugging facility, what kind of bug is it to supply a zero-sized file as an argument?  
  
A bug possible to be introduced by authors while writing GIL IO tests, perhaps. The assertions might have been left intentionally or as an intermediate solution perhaps or by mistake.  I can only guess.   
  
> I stand by my statement that one should never fail an assertion on a runtime condition.  
  
I believe _I did agree with you on that_ and I confirmed the code needs improvements.  
  
> I understand that you, not being the author, don't want to continue the discussion anymore.   
  
I just don't have enough knowledge to contribute anything meaningful to this discussion, apart from the realisation (after your further clarification of what you are doing) and agreement the current code in GIL needs to be improved.  
  
There are (very) many areas of GIL that need attention. I just don't have time to fix your problem.  
I am **a volunteer**. **I heavily depend on help** from other GIL users and developers interested in GIL, like yourself.  
  
Since you seem to have a very clear picture of how that should be improved, I suggested you to submit a pull request. Given the lengths our debate has gone to, this could have already been fixed, contributed and merged.  
  
> Hopefully someone from them will be available to make further comments.  
  
I don't see any need for further comments. We've reached common ground the code needs improvements. Now it's just a matter to get things done. I encourage you to give it a try!

---

## Comment 10

> Username: christian-henning  
> Created at: 2019-08-05 21:52:22 UTC  
> Url: https://github.com/boostorg/gil/issues/360#issuecomment-518414840  

@mloskot   
  
I think zero length should be allowed and should create an empty png with correct header, aka width and height set to 0.  
  
I'm not sure that answers your question. If it doesn't please send me an email.  
  
Also, sorry for all the bugs in IO. :-/

---

## Comment 11

> Username: mloskot  
> Created at: 2019-08-06 13:26:01 UTC  
> Url: https://github.com/boostorg/gil/issues/360#issuecomment-518668413  

@christian-henning, no reason to be sorry; bugs are intrinsic part of any activity that involves writing a code :-)  
  
It is not uncommon to make use of assertions at various stages of development and testing, for anything from as comments through algorithm intermediate check-points to algorithms inputs, similarly to the [checked iterators](https://docs.microsoft.com/en-us/cpp/standard-library/checked-iterators?view=vs-2019), because it's generally understood that assertions [resemble comments: no code will be generated from them](https://akrzemi1.wordpress.com/2011/04/06/assertions/). You can find them checking inputs even in example from authoritative resources like the CppCoreGuidelines: `void advance(ForwardIterator p, int n) { assert(n >= 0); while (n--) ++p;}`.  
  
There is no single straight jacket of Do-s and Dont-s that fits all in all circumstances.  
So, Christian, please don't feel too bad about it.  
  
-----  
  
p.s. @chhenning == @christian-henning ?

---

## Comment 12

> Username: christian-henning  
> Created at: 2019-08-06 15:32:26 UTC  
> Url: https://github.com/boostorg/gil/issues/360#issuecomment-518722110  

Yes, the same person.

---

## Comment 13

> Username: xjhouska  
> Created at: 2019-08-07 12:15:09 UTC  
> Url: https://github.com/boostorg/gil/issues/360#issuecomment-519070267  

So I went ahead, modified the code, and created the pull request. Although I have done lots of C++ coding and coding in general before, this is my first experience with GitHub and my first contribution to Boost. So please bear with me. Thanks, Jan

---

## Comment 14

> Username: mloskot  
> Created at: 2019-08-07 18:09:51 UTC  
> Updated at: 2019-08-07 18:10:15 UTC  
> Url: https://github.com/boostorg/gil/issues/360#issuecomment-519209502  

@xjhouska Awesome! Thank you very much for your first contribution to GIL, and Boost!  
I hope to review it by tomorrow, unless @chhenning or @stefanseefeld review and approve it earlier.

---

## Comment 15

> Username: mloskot  
> Created at: 2019-08-09 08:05:56 UTC  
> Url: https://github.com/boostorg/gil/issues/360#issuecomment-519823612  

Fixed by @xjhouska in PR #369 Thanks!
