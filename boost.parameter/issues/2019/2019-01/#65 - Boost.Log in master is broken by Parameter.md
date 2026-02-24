# #65 - Boost.Log in master is broken by Parameter [Closed]

> Username: Lastique  
> Created at: 2019-01-16 09:16:30 UTC  
> Updated at: 2019-02-08 19:29:16 UTC  
> Closed at: 2019-01-19 16:24:46 UTC  
> Url: https://github.com/boostorg/parameter/issues/65  

Windows CI jobs reported [compilation errors](https://ci.appveyor.com/project/Lastique/log/builds/21628319/job/dbdseb9nhl3kb2te?fullLog=true#L1616) for MSVC 14.1. Also, there is a similar [report](https://github.com/boostorg/log/issues/72) from users.

---

## Comment 1

> Username: eldiener  
> Created at: 2019-01-16 14:23:30 UTC  
> Url: https://github.com/boostorg/parameter/issues/65#issuecomment-454796822  

The 'master' branch of Parameter has not changed since 10/15/2018 and the only changes to that branch were when Peter Dimov split parameter_python from parameter, which did not change the parameter header files.

---

## Comment 2

> Username: Lastique  
> Created at: 2019-01-16 14:42:43 UTC  
> Url: https://github.com/boostorg/parameter/issues/65#issuecomment-454803483  

The same Boost.Log commit on develop [passes](https://ci.appveyor.com/project/Lastique/log/builds/21609579) MSVC CI (don't mind the MinGW failure, it's an unrelated issue). The failure on master appeared when I merged Boost.Log develop to master recently.  
  
The important change in Boost.Log that was merged to master was [this](https://github.com/boostorg/log/commit/543404002f44a10916a0c9de306006acff13230b) commit (originated from https://github.com/boostorg/log/pull/68). As I understand, this change was needed due to changes by @CromwellEnage in Boost.Parameter that were not merged to master. I'm seeing quite a lot of differences between current master and develop of Boost.Parameter, not sure what was the commit that required the change in Boost.Log.  
  
I think, merging Boost.Parameter to master should solve the problem.

---

## Comment 3

> Username: eldiener  
> Created at: 2019-01-16 14:57:39 UTC  
> Url: https://github.com/boostorg/parameter/issues/65#issuecomment-454808730  

As I understand it from Cromwell's changes there are still a number of issues syncing up changes in 'develop' in Parameter with other libraries in 'develop', such as 'convert', which depended on non-public parts of Parameter. I have not merged Parameter 'develop' to 'master' because of these still unresolved issues. I wish you had not merged any commit in Log from 'develop' to 'master' which depended on Parameter 'develop' changes which Cromwell had made, because I think such merging needs to be synced together and I do not think this can be done until all issues around the Parameter 'develop' changes have been resolved. Hopefully Cromwell will jump into this discussion and explain what issues still remain, vis-a-vis other libraries which depend on Parameter non-public parts, before I merge Parameter 'develop' to 'master'.

---

## Comment 4

> Username: Lastique  
> Created at: 2019-01-16 15:04:44 UTC  
> Url: https://github.com/boostorg/parameter/issues/65#issuecomment-454811341  

Not merging the commit selectively is problematic for me as I try to maintain master and develop in sync (i.e. master always refers to some commit from develop). If the changes in Boost.Parameter cannot be merged, is there a way to make a single version of Boost.Log be compatible with with both Boost.Parameter master and develop?

---

## Comment 5

> Username: CromwellEnage  
> Created at: 2019-01-16 17:29:11 UTC  
> Url: https://github.com/boostorg/parameter/issues/65#issuecomment-454867209  

@Lastique, for now you can edit <boost/log/utility/setup/file.hpp> by replacing line 106 with:  
  
``  
#if defined(BOOST_PARAMETER_HAS_PERFECT_FORWARDING) || \  
    defined(BOOST_PARAMETER_COMPOSE_MAX_ARITY)  
``  
``  
inline typename parameter::aux::tag< keywords::tag::file_name, T const& >::type  
``  
``  
#else  
``  
``  
inline typename parameter::aux::tag< keywords::tag::file_name, T const >::type  
``  
``  
#endif  
``  
  
Those preprocessor tokens are only available in the develop version, and I don't plan on touching them any time soon.

---

## Comment 6

> Username: CromwellEnage  
> Created at: 2019-01-16 18:21:31 UTC  
> Url: https://github.com/boostorg/parameter/issues/65#issuecomment-454885477  

@eldiener, as per the message I recently sent out to the developers' list, I have active PRs for the following libraries, with their relevant issues in parentheses:  
  
* Boost.Accumulators (duplicate definition of BOOST_PARAMETER_NESTED_KEYWORD, use of deprecated facilities)  
* Boost.Convert (use of non-public facilities)  
* Boost.Log (as explained by Andrey Semashev)

---

## Comment 7

> Username: eldiener  
> Created at: 2019-01-16 19:17:16 UTC  
> Url: https://github.com/boostorg/parameter/issues/65#issuecomment-454904312  

Can you, or, have you, created public facilities to replace the non-public facilities previously used by any of the above libraries ? If you have I will push each of these libraries to accept your PRs. If you have not, can you please create a public facility to replace the non-public use of Parameter in the above libraries ?

---

## Comment 8

> Username: CromwellEnage  
> Created at: 2019-01-16 20:06:26 UTC  
> Url: https://github.com/boostorg/parameter/issues/65#issuecomment-454921026  

I have created public facilities to replace usage of the non-public  
facilities.  All other PRs make use of the public facilities.  Only for  
Boost.Log have I made an exception, but I can submit a later PR that will  
make use of the new BOOST_PARAMETER_NO_SPEC_* code generation macros.  
  
On Wed, Jan 16, 2019 at 14:17 Edward Diener <notifications@github.com>  
wrote:  
  
> Can you, or, have you, created public facilities to replace the non-public  
> facilities previously used by any of the above libraries ? If you have I  
> will push each of these libraries to accept your PRs. If you have not, can  
> you please create a public facility to replace the non-public use of  
> Parameter in the above libraries ?  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/parameter/issues/65#issuecomment-454904312>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AfcJsD92xP5GLp8I3koV4c6Axs0kfupuks5vD3q9gaJpZM4aCn7u>  
> .  
>

---

## Comment 9

> Username: Lastique  
> Created at: 2019-01-16 21:15:18 UTC  
> Url: https://github.com/boostorg/parameter/issues/65#issuecomment-454944570  

I have added a workaround that should solve the problem for Boost.Log. At least it does in my local testing. It's still in develop, but once the tests pass I'll merge it to master. I suppose, this ticket can be closed, unless you want to have it as a reminder to merge Boost.Parameter to master.

---

## Comment 10

> Username: eldiener  
> Created at: 2019-01-16 23:49:56 UTC  
> Url: https://github.com/boostorg/parameter/issues/65#issuecomment-454988237  

Yes, please keep this ticket open. I want to coordinate, with Cromwell and other library developers, an eventual merge of Parameter 'develop' to 'master' so that other libraries which depend on Parameter can merge at the same time. But first we need to get Convert and Accumulators to accept Cromwell's PRs for 'develop'. I assume Log is now OK. If you feel that you need Cromwell to create a PR for log as he has specified above, please say so, else I will assume that Log is OK as is.

---

## Comment 11

> Username: eldiener  
> Created at: 2019-01-17 03:39:09 UTC  
> Url: https://github.com/boostorg/parameter/issues/65#issuecomment-455031039  

Does the PR for convert use a Parameter public interface to replace the non-public interface it previously used ? If so can you please make that argument in the comments for the PR for convert, as the convert maintainer is basically arguing that he does not want to replace the non-public interface that was previously used with another non-public interface in your PR..

---

## Comment 12

> Username: CromwellEnage  
> Created at: 2019-01-17 04:43:21 UTC  
> Url: https://github.com/boostorg/parameter/issues/65#issuecomment-455040548  

I've left a comment along with some statements addressing his latest concerns.

---

## Comment 13

> Username: Lastique  
> Created at: 2019-01-17 15:02:50 UTC  
> Url: https://github.com/boostorg/parameter/issues/65#issuecomment-455202896  

> I assume Log is now OK.  
  
Yes, today MSVC CI passed on master for Boost.Log.

---

## Comment 14

> Username: Lastique  
> Created at: 2019-02-08 18:39:16 UTC  
> Updated at: 2019-02-08 18:39:43 UTC  
> Url: https://github.com/boostorg/parameter/issues/65#issuecomment-461902507  

There appears to be a new change in Boost.Parameter develop that [broke](https://travis-ci.org/boostorg/log/jobs/490575404#L4558) Boost.Log. The problem is that the [`enable_if_named_parameters`](https://github.com/boostorg/log/blob/c0d0fd33f325c818589e11d55af4636ef1a3b17c/include/boost/log/detail/parameter_tools.hpp#L124-L143) helper in Boost.Log fails to detect the new type of named parameters that is `tagged_argument_list_of_1`. This wrapper is only present in develop, and as before, I need Boost.Log to be compatible with both develop and master of Boost.Parameter. Any suggestions on how we could handle it?

---

## Comment 15

> Username: Lastique  
> Created at: 2019-02-08 19:27:11 UTC  
> Url: https://github.com/boostorg/parameter/issues/65#issuecomment-461917489  

Nevermind, I think I found a solution.

---

## Comment 16

> Username: CromwellEnage  
> Created at: 2019-02-08 19:28:49 UTC  
> Updated at: 2019-02-08 19:29:16 UTC  
> Url: https://github.com/boostorg/parameter/issues/65#issuecomment-461918026  

Okay.  I was going to say:  
  
The new type `boost::parameter::aux::tagged_argument_list_of_1` is defined iff `BOOST_PARAMETER_CAN_USE_MP11` is defined.  It inherits from its template parameter, which is an instantiation of either `boost::parameter::aux::tagged_argument` or `boost::parameter::aux::tagged_argument_rref` (the latter is defined only if `BOOST_PARAMETER_HAS_PERFECT_FORWARDING` is defined).  At a minimum, you'll need to add:  
  
```  
#if defined(BOOST_PARAMETER_CAN_USE_MP11)  
template< typename TaggedArg, typename R >  
struct enable_if_named_parameters<  
  boost::parameter::aux::tagged_argument_list_of_1< TaggedArg >, R  
> : enable_if_named_parameters< TaggedArg, R >  
{  
};  
#endif  
```
