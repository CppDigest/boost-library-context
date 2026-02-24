# #97 - Default parameter value used instead of the specified one [Closed]

> Username: Lastique  
> Created at: 2020-03-03 15:31:04 UTC  
> Updated at: 2020-10-02 13:20:51 UTC  
> Closed at: 2020-03-23 15:07:25 UTC  
> Url: https://github.com/boostorg/parameter/issues/97  

Consider this test case:  
  
```  
#include <string>  
#include <iostream>  
#include <boost/parameter/keyword.hpp>  
  
namespace keywords {  
  
BOOST_PARAMETER_KEYWORD(tag, file_name)  
  
}  
  
void construct_impl(std::string const& str)  
{  
    std::cout << "str: " << str << std::endl;  
}  
  
template< typename Args >  
void construct(Args const& args)  
{  
    construct_impl(args[keywords::file_name | std::string()]);  
}  
  
int main()  
{  
    construct((keywords::file_name = "sample.log"));  
}  
```  
  
Currently, it prints "str: " instead of "str: sample.log". Boost.Parameter uses the default-constructed `std::string` value to pass to `construct_impl` instead of the one passed by user.  
  
This causes regressions in Boost.Log (https://github.com/boostorg/log/issues/104) and is reportedly started happening somewhere between Boost 1.69 and 1.71.  
  
PS: Reproduced with gcc 9 and VS2015, but probably not only those compilers.

---

## Comment 1

> Username: urandon  
> Created at: 2020-03-03 16:11:09 UTC  
> Url: https://github.com/boostorg/parameter/issues/97#issuecomment-594031509  

@CromwellEnage is there any progress with issues #91 #95 #97? Should be release 1.71 and 1.72 be considered as totally broken and 1.70 is the only recent stable?

---

## Comment 2

> Username: Lastique  
> Created at: 2020-03-13 11:01:05 UTC  
> Url: https://github.com/boostorg/parameter/issues/97#issuecomment-598667358  

@CromwellEnage Please advise on the current plan to fix this issue. I don't want this kind of problem to be released in Boost.Log 1.73. If the problem cannot be fixed before 1.73, can we at least roll back Boost.Parameter to pre-1.71 state?

---

## Comment 3

> Username: glenfe  
> Created at: 2020-03-22 23:27:53 UTC  
> Url: https://github.com/boostorg/parameter/issues/97#issuecomment-602294107  

@Lastique It's clear Cromwell Enage isn't responding. Could you please prepare a PR to revert his changes back to pre-1.71 state? I'll take care of merging and shepherding them through the 1.73 release.

---

## Comment 4

> Username: Lastique  
> Created at: 2020-03-22 23:34:18 UTC  
> Url: https://github.com/boostorg/parameter/issues/97#issuecomment-602297292  

@glenfe So, will https://github.com/boostorg/parameter/pull/99 be abandoned then? Should I close it?

---

## Comment 5

> Username: glenfe  
> Created at: 2020-03-22 23:36:02 UTC  
> Url: https://github.com/boostorg/parameter/issues/97#issuecomment-602298073  

@Lastique I think so. #99 fixes that one issue, while rolling back everything fixes more, right?

---

## Comment 6

> Username: Lastique  
> Created at: 2020-03-22 23:39:58 UTC  
> Url: https://github.com/boostorg/parameter/issues/97#issuecomment-602298646  

Yes. I was just thinking about merging it to develop first, then moving the result to a separate branch before reverting to 1.70.

---

## Comment 7

> Username: Lastique  
> Created at: 2020-03-23 00:27:44 UTC  
> Url: https://github.com/boostorg/parameter/issues/97#issuecomment-602305576  

@glenfe Created https://github.com/boostorg/parameter/pull/100.

---

## Comment 8

> Username: sehe  
> Created at: 2020-09-03 20:44:08 UTC  
> Url: https://github.com/boostorg/parameter/issues/97#issuecomment-686755267  

I think this reintroduced #97, see #104? I'm lost in the web of reverts and coagulated merges. Please advise.

---

## Comment 9

> Username: Lastique  
> Created at: 2020-09-04 06:19:51 UTC  
> Url: https://github.com/boostorg/parameter/issues/97#issuecomment-686937810  

The revert to 1.70 state was eventually abandoned as it removed some functionality that was used in downstream libraries. The fix for this bug was merged to develop in https://github.com/boostorg/parameter/pull/99. But apparently it was not merged to master so it was not released. @glenfe Could you merge to master, please?

---

## Comment 10

> Username: glenfe  
> Created at: 2020-09-05 17:53:01 UTC  
> Url: https://github.com/boostorg/parameter/issues/97#issuecomment-687641791  

@Lastique done.

---

## Comment 11

> Username: VladAdGad  
> Created at: 2020-10-02 13:20:51 UTC  
> Url: https://github.com/boostorg/parameter/issues/97#issuecomment-702730381  

Still in 1.74 same issue
