# #39 Update boost::cnv::cnvbase and boost::cnv::basic_stream [Merged]

> Username: CromwellEnage  
> Created at: 2018-11-12 02:59:42 UTC  
> Updated at: 2019-01-17 20:01:10 UTC  
> Merged at: 2019-01-17 19:55:07 UTC  
> Closed at: 2019-01-17 19:55:07 UTC  
> Url: https://github.com/boostorg/convert/pull/39  

Use public interface of Boost.Parameter vice boost::parameter::aux::tag.

---

## Comment 1

> Username: yet-another-user  
> Created_at: 2018-11-12 21:52:52 UTC  
> Url: https://github.com/boostorg/convert/pull/39#issuecomment-438041315  

Thanks for adjusting your branch to incorporate the BOOST_PARAMETER_NAME-related changes I made. Much appreciated. I was about to merge your fix when I remembered that for it to compile I have to check-out the boost/parameter/develop as well. Having to do that appears to be a royal pain as I already have/use an official Boost distribution for every-day work. So, having two distributions and making sure the correct one is deployed is, well, uncomfortably error-prone. If you think it might be possible to eliminate the deployment of is_argument_pack and, consequently, rely on the official distribution (rather than parameter/develop), that'd be great and I'd merge your changes in without the fear of them breaking my every-day environment. I suspect the need for is_argument_pack comes from your choice to support argument packs in operator(). The official "convert" version does _not_ support those and IMO it's not such a big deal. If you think you might make that adjustment, I'd be happy to incorporate your changes. Otherwise, I've parked/saved your branch locally until the time I can merge it without jumping through the parameter/develop hoops.

---

## Comment 2

> Username: CromwellEnage  
> Created_at: 2018-11-12 23:51:56 UTC  
> Url: https://github.com/boostorg/convert/pull/39#issuecomment-438074785  

I've made an implementation-defined version of is_argument_pack in namespace boost::cnv::parameter_detail.  
  
In the current release distribution of Boost, argument packs do not model the MPL Associative Sequence concept, so boost::mpl::has_key can't be used, but I've worked around that as well.  
  
I edited the Jamfile so that the tests will pass in Travis, but I've verified that the workarounds work in my local environment.

---

## Comment 3

> Username: yet-another-user  
> Created_at: 2018-11-13 00:24:53 UTC  
> Url: https://github.com/boostorg/convert/pull/39#issuecomment-438081338  

Cromwell, your efficiency and enthusiasm are every impressive. I mean it... and I do feel you are trying to move "convert" forward in the right direction. Having said that I honestly feel fairly uncomfortable as your changes keep mounting at an alarming rate... without IMO bringing us closer the original goal. Let's step back a little bit and remember that the original change/merge request was about _replacing_ internal boost::parameter::aux API with public boost::parameter API (or so I understood). Now in your latest workaround I see that you resort to that very internal API (stream.hpp). Doing so renders the whole effort questionable as it adds a lot of complexity without achieving the original objective. Do you agree? So far, I feel that either a) the changes need to be parked until is_argument_pack functionality is officially available or b) for the time being you remove reliance on that functionality. I feel "b)" _might_ be doable/preferrable as is_argument_pack is only needed because you chose operator() to work with arg-packs. I am not convinced that that is a good idea as calls   
  
cnv(arg::base = boost::cnv::base::dec)(arg::fill = ' ')(arg::adjust = cnv::adjust::right)  
  
seem inefficient. The current implementation has operator() overloaded on boost::parameter::aux::tag. Can we have it using public boost::parameter API? Apologies if I might come across as pain in a neck. :-)

---

## Comment 4

> Username: CromwellEnage  
> Created_at: 2018-11-13 02:38:14 UTC  
> Url: https://github.com/boostorg/convert/pull/39#issuecomment-438108244  

Do you think I forgot what I said?  Then you _are_ being a pain in the neck.  
  
If you're looking for a direct public API version of boost::parameter::aux::tag, the current Boost release doesn't have one.  That's why I created boost::parameter::is_argument_pack in the first place.  However, since it's only in the development branch and your work requirements render it unusable, I can only conclude that we are at an impasse.  
  
Therefore, I'm reverting to the original content of this workaround, and we can go with option a).  
  
BTW, the new functionality would allow calls such as:  
  
cnv((arg::base = boost::cnv::base::dec, arg::fill = ' ', arg::adjust = cnv::adjust::right))  
  
This is much more efficient than the call in your last comment.  
  
Boost.Parameter has always provided argument-composition functionality.  I was not available to participate in the review of Boost.Convert, or else I would have argued to use that functionality rather than rolling out its own.  But that is neither here nor there.

---

## Comment 5

> Username: yet-another-user  
> Created_at: 2018-11-13 02:50:34 UTC  
> Url: https://github.com/boostorg/convert/pull/39#issuecomment-438110917  

You sound frustrated. :-) No need to be... as there is no need for aggravation... Really... Although sooner or later you'll realize and will have to learn to accept that it might well be that other people do not rush to accept and/or embrace what you have to offer... or your work does not map ideally onto other peoples' framework and/or procedures. I am sure it'll be the same if our positions are reversed.  
  
As for cnv((...)), then I did/do realize it is an additional functionality and, yes, it is more efficient. However, I find double brackets questionable. I understand what it means. I myself use it all the time... in my own code. However, other peoples' mileage might differ.    
  
As for "I was not available to participate in the review of Boost.Convert, or else", then I am sure there are still plenty reviews coming up. Boost.Convert is just a drop in the Boost ocean. Thank you for your interest, effort and contribution. It is much appreciated. I just feel that incorporating functionality not officially available is premature. You clearly disagree. No drama.

---

## Comment 6

> Username: eldiener  
> Created_at: 2018-12-03 22:50:03 UTC  
> Url: https://github.com/boostorg/convert/pull/39#issuecomment-443900820  

I just want to point out, without arguing one side or other of the case, that having to check out the 'develop' branch of a library in order to approve of a PR is not a valid reason IMO for not applying a PR. Maintainers are expected to check out the 'develop' branch of any library simply because that is the branch for which all PRs are created, and the usual workflow is to apply PRs to the 'develop' branch, make sure the changes test correctly either/or through CI testing and regression testing, and then merge those 'develop' branch changes to 'master'.

---

## Comment 7

> Username: yet-another-user  
> Created_at: 2018-12-03 23:15:03 UTC  
> Url: https://github.com/boostorg/convert/pull/39#issuecomment-443907176  

> I just want to point out... that having to check out the 'develop' branch of a library in order to approve of a PR is not a valid reason IMO for not applying a PR.   
  
Edward, thank you for your comment. Appreciated. The only issue I have with that approach is that the "develop" branch is an unpublished, developer's internal branch... and there are no guarantees (or so I understand) that the work/changes in that branch will ultimately make it to the master branch. That uncertainty makes it quite possible that my library will break if I rely on some unpublished (only in "develop") functionality but its developer either changes the functionality or postpones publishing it to master.

---

## Comment 8

> Username: eldiener  
> Created_at: 2018-12-04 00:07:28 UTC  
> Url: https://github.com/boostorg/convert/pull/39#issuecomment-443918659  

I think you are wrong in your view of the 'develop' branch for Boost repositories. The 'develop' branch might of course contain incorrect code, but it gets full testing in the Boost regression testing matrix   
( https://www.boost.org/development/tests/develop/developer/summary.html ) and is expected to be used as a stepping stone to updating the 'master' branch. If you are a maintainer you really need to be able to test the 'develop' branch of the library(s) you help maintain. Not doing so is really unacceptable for a maintainer of a library.

---

## Comment 9

> Username: yet-another-user  
> Created_at: 2018-12-04 00:39:06 UTC  
> Url: https://github.com/boostorg/convert/pull/39#issuecomment-443925226  

I was not really talking about testing the 'develop' branch. My point was  
that the 'develop' branch is for development and, therefore, is subject to  
change and is not guaranteed to be merged in its current form (or any form)  
to the master branch. If that is indeed so, then relying on any  
functionality available in somebody else's 'develop' branch is potentially  
troublesome.  
  
On Tue, Dec 4, 2018 at 11:07 AM Edward Diener <notifications@github.com>  
wrote:  
  
> I think you are wrong in your view of the 'develop' branch for Boost  
> repositories. The 'develop' branch might of course contain incorrect code,  
> but it gets full testing in the Boost regression testing matrix  
> ( https://www.boost.org/development/tests/develop/developer/summary.html  
> ) and is expected to be used as a stepping stone to updating the 'master'  
> branch. If you are a maintainer you really need to be able to test the  
> 'develop' branch of the library(s) you help maintain. Not doing so is  
> really unacceptable for a maintainer of a library.  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/convert/pull/39#issuecomment-443918659>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ABswIKl0C4C7djIRk5V-dZtRACJ3EENfks5u1bzAgaJpZM4YY3yk>  
> .  
>

---

## Comment 10

> Username: eldiener  
> Created_at: 2018-12-07 06:37:53 UTC  
> Url: https://github.com/boostorg/convert/pull/39#issuecomment-445138022  

Yes it is possible that a 'develop' branch change in some library will not make it into 'master' in its current form. But in that case any further change to the 'develop' branch of that library may incorporate further changes,  most often in the form of anothe PR, to some other library, that depends on that library. Normally changes to a library, even in the 'develop' branch, should not break another library which uses the first library. But in this case the break occurs because convert is using a non-public portion of Boost parameter. Would you agree too that, and that using a non-public and non-documented API from another library, which is part of the internals of that other library, is a bad thing ? So those internals have changed for Boost parameter, without breaking the public API, but you do not want to update Boost convert to correct its use of those very same internals of Boost parameter. I do not understand your position. The 'develop' branch and the 'master' branch of a library can be different and the library can still be valid. If you think Cromwell can offer a better public interface for your use of Boost Parameter, I can at least understand that, but the rest of your argument about your unwillingness to update 'develop' because, for the time being, it will be different from what is in 'master' vis-a-vis Boost Parameter, makes little sense to me.

---

## Comment 11

> Username: yet-another-user  
> Created_at: 2018-12-07 07:15:08 UTC  
> Url: https://github.com/boostorg/convert/pull/39#issuecomment-445144588  

RE: the break occurs because convert is using a non-public portion of Boost  
parameter...  
  
I did not know and I was not aware of any break occurring. Is there a break?  
  
RE: Would you agree ... that using a non-public and non-documented API from  
another library... is a bad thing  
  
Sure. I do not believe I ever argued the opposite and I worked with  
Cromwell to incorporate his changes... for the purpose (as he stated)  
of *replacing  
non-public API with the public one*... until I realized that his proposed  
solution was not doing that (see more below).  
  
RE: ... but you do not want to update Boost convert to correct its use of  
those very same internals of Boost parameter. I do not understand your  
position.  
  
I stated my position and the reason - "... the original change/merge  
request was about *replacing* internal boost::parameter::aux API with  
public boost::parameter API... *Now in your latest workaround I see that  
you resort to that very internal API*".  
  
The above shows that Cromwell's proposal was not (or so I understood)  
*replacing  
non-public with public* but rather replacing one implementation using  
non-public with some other implementation still using non-public. I did not  
see great value/benefit/advantage in doing that. Does it make sense?  
  
RE: ...your unwillingness to update 'develop' because, for the time being,  
it will be different from what is in 'master' vis-a-vis Boost Parameter,  
makes little sense to me.  
  
Again, I'll re-iterate that boost::parameter "develop" is a development  
branch, work-in-progress and subject to change. Changes may or may not make  
it to 'master'. Yes, I do hear your arguments that changes in the 'develop'  
branch should be orderly and all. I am not as optimistic.  
  
  
On Fri, Dec 7, 2018 at 5:37 PM Edward Diener <notifications@github.com>  
wrote:  
  
> Yes it is possible that a 'develop' branch change in some library will not  
> make it into 'master' in its current form. But in that case any further  
> change to the 'develop' branch of that library may incorporate further  
> changes, most often in the form of anothe PR, to some other library, that  
> depends on that library. Normally changes to a library, even in the  
> 'develop' branch, should not break another library which uses the first  
> library. But in this case the break occurs because convert is using a  
> non-public portion of Boost parameter. Would you agree too that, and that  
> using a non-public and non-documented API from another library, which is  
> part of the internals of that other library, is a bad thing ? So those  
> internals have changed for Boost parameter, without breaking the public  
> API, but you do not want to update Boost convert to correct its use of  
> those very same internals of Boost parameter. I do not understand your  
> position. The 'develop' branch and the 'master' branch of a library can be  
> different and the library can still be valid. If you think Cromwell can  
> offer a better public interface for your use of Boost Parameter, I can at  
> least understand that, but the rest of your argument about your  
> unwillingness to update 'develop' because, for the time being, it will be  
> different from what is in 'master' vis-a-vis Boost Parameter, makes little  
> sense to me.  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/convert/pull/39#issuecomment-445138022>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ABswIII4k0c0pILlNRI44TY7c8tD9on7ks5u2gzBgaJpZM4YY3yk>  
> .  
>

---

## Comment 12

> Username: CromwellEnage  
> Created_at: 2019-01-17 04:38:04 UTC  
> Url: https://github.com/boostorg/convert/pull/39#issuecomment-455039786  

This PR, as it stands, uses the public API of Boost.Parameter in the 'develop' branch.  (The *latest workaround* you refer to that accesses an internal API has been reverted and no longer exists, so please stop referring to it.)  
  
I do not treat the 'develop' branch of Boost.Parameter as my own personal development branch.  (I have my own fork and its various branches for that purpose.)  I treat it as a repository of changes that have been accepted by the maintainers and will be merged into 'master' at some appointed time, then into the next available release.  
  
The only steps that need to be taken before the changes in Boost.Parameter will be merged into 'master' are the acceptance and merging of this PR and that of the one in Boost.Accumulators.

---

## Comment 13

> Username: eldiener  
> Created_at: 2019-01-17 15:36:05 UTC  
> Updated_at: 2019-01-17 15:37:43 UTC  
> Url: https://github.com/boostorg/convert/pull/39#issuecomment-455215437  

Cromwell has provided a PR which uses a public API of the current Parameter on the 'develop' branch, in place of the non-public use of Parameter which the Convert library currently uses. I would like to merge his fixes to the Parameter 'master' branch so that his improvements to Parameter, which are extensive, go into the next release of Boost. Cromwell has been very careful not to break the current public API of Parameter while adding a host of useful features to Parameter, especially for those using c++11 on up, which improve usage of the Parameter library greatly.  
  
If you think some other specific public API in Parameter should be created to replace the non-public usage of Parameter which Convert currently has, please argue for that. Otherwise please merge this fix to the 'develop' branch of 'Convert'. When I merge the 'develop' branch of Parameter to to the 'master' branch of Parameter I will coordinate it with Convert and any other library which also needs to merge its 'develop' commits to 'master' at the same time so that its 'master' branch is in sync with the changes in Parameter's 'master' branch.  
  
Let me reiterate the basic fact that libraries should not be using non-public interfaces of other libraries, because developers should be able to change the internals of their library without breaking their library's use by other libraries. I do understand that this happens, especially among Boost developers. But I am appealing to you to consider that changing the internals of a library for both better design and better functionality is a library's prerogative and that the problem that this has caused for Convert is not Parameter's fault but Convert's fault instead. Please take the necessary actions so we can move ahead with improving Parameter without breaking Convert or any other library which depends on Parameter.

---

## Comment 14

> Username: eldiener  
> Created_at: 2019-01-17 20:01:10 UTC  
> Url: https://github.com/boostorg/convert/pull/39#issuecomment-455312154  

Thanks !

---
