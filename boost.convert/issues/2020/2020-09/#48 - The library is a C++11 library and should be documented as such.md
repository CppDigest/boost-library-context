# #48 - The library is a C++11 library and should be documented as such [Closed]

> Username: eldiener  
> Created at: 2020-09-12 19:15:11 UTC  
> Updated at: 2020-09-13 08:01:43 UTC  
> Closed at: 2020-09-13 08:01:43 UTC  
> Url: https://github.com/boostorg/convert/issues/48  

Since the library uses C++11 type_traits, along with Boost type_traits, the library is a C++11 library and should be documented as such. I actually see no reason to use both C++11 type_traits as well as Boost type_traits. If it is a C++11 library why not use C++11 type_traits throughout the library ?

---

## Comment 1

> Username: yet-another-user  
> Created at: 2020-09-12 20:49:35 UTC  
> Url: https://github.com/boostorg/convert/issues/48#issuecomment-691545873  

Hi, Ed, nice hearing from you. Thank you for the input. Appreciated. I ma likely to have fallen behind the latest Boost requirements wrt the documentation. Would you mind elaborating what your expectations are exactly. Why explicitly state C11? Are you implying it is not usable with C14 and up? As for "why not use C++11 type_traits throughout", then indeed that'd be natural today. Back then I started that transition but did not feel urgency following it through. Is there fundamental reason to do that or it is a "neatness" issue? Tnx.

---

## Comment 2

> Username: eldiener  
> Created at: 2020-09-12 21:20:02 UTC  
> Url: https://github.com/boostorg/convert/issues/48#issuecomment-691550876  

If you are using C++11 type_traits clearly the library is a C++11 on up library. I think you should document this, else others will think the library might be usable at the C++03 level. That is all I suggest. Boost is deprecating use of libraries at the C++03 level, but that does not mean that libraries which require the C++11 level or upward should not document their standard C++ level requirements, because even though Boost is deprecating the C++03 level a great number of libraries still work fine at that level ( and of course at C++11, C++14, C++17, and C++20 also ). Boost has officially said that any library which currently still works at the C++03 level can just upgrade themselevs to C++11 as they wish, so you have done nothing wrong making 'convert' a C++11 library.

---

## Comment 3

> Username: yet-another-user  
> Created at: 2020-09-12 21:33:59 UTC  
> Url: https://github.com/boostorg/convert/issues/48#issuecomment-691553144  

Ed, thank you for your promptly reply and explanations. Appreciated. Makes sense... although I'd be surprised to know that there are still are people, projects on  C++03. :-) I'll clarify that in the docs.   
  
While we are here pls let me ask something. I see "master" and "develop" branches different. If I remember correctly, the change was due to your request awhile ago. Is the change in "develop" still relevant? Should I merge it into "master"?.. or dump it? Do you know? Tnx.

---

## Comment 4

> Username: eldiener  
> Created at: 2020-09-12 22:42:58 UTC  
> Url: https://github.com/boostorg/convert/issues/48#issuecomment-691565462  

About what change in 'develop' are you speaking ? Some changes have been made between 'master' and 'develop' but not by me.

---

## Comment 5

> Username: yet-another-user  
> Created at: 2020-09-12 23:00:16 UTC  
> Url: https://github.com/boostorg/convert/issues/48#issuecomment-691567887  

Yes, indeed. I only realized that I might have not been sufficiently clear after posting. I am referring to the issue #39 initiated by Cromwell and related to some boost::parameter API usage. Is it still relevant?.. as that change does not seem in the "master" which seems to work just fine. Just trying to refresh my memory and sync the branches.

---

## Comment 6

> Username: yet-another-user  
> Created at: 2020-09-13 05:53:59 UTC  
> Url: https://github.com/boostorg/convert/issues/48#issuecomment-691618802  

Namely, my question relates to your following comment: "When I merge the 'develop' branch of Parameter to to the 'master' branch of Parameter I will coordinate it with Convert and any other library which also needs to merge its 'develop' commits to 'master' at the same time". That coordination seems to never happened (t omy knowledge). Does it negate the parameter-related changes that I merged into Convert at your request?

---

## Comment 7

> Username: yet-another-user  
> Created at: 2020-09-13 08:01:43 UTC  
> Url: https://github.com/boostorg/convert/issues/48#issuecomment-691631315  

All sorted out. I see the features from boost/parameter available in the master. So, I merged the updated develop branch in the master branch as well. And 'yes' added "required C++11 and up" is the docs as you mentioned. Thanks for your input. Closing.
