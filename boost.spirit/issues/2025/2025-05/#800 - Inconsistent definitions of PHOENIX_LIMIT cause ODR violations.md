# #800 - Inconsistent definitions of PHOENIX_LIMIT cause ODR violations [Closed]

> Username: pdimov  
> Created at: 2025-05-06 19:25:06 UTC  
> Updated at: 2025-05-10 00:20:24 UTC  
> Closed at: 2025-05-07 23:24:41 UTC  
> Url: https://github.com/boostorg/spirit/issues/800  

As reported in https://github.com/boostorg/quickbook/issues/27, use of Spirit (Classic) causes ODR violations depending on include order. If `<boost/spirit/include/classic_attribute.hpp>` is included, as happens [here](https://github.com/boostorg/quickbook/blob/fea2283c87e59d6c9d8f12692f0c39987076c56f/src/main_grammar.cpp#L11), then `PHOENIX_LIMIT` is defined to 6 here:  
  
https://github.com/boostorg/spirit/blob/dcaded05c84db5ecea6485178f77e825df5352f1/include/boost/spirit/home/classic/attribute.hpp#L30  
  
However, if `<boost/spirit/include/phoenix1_tuples.hpp>` is included without the above include, then `PHOENIX_LIMIT` is defined to 3 here:  
  
https://github.com/boostorg/spirit/blob/dcaded05c84db5ecea6485178f77e825df5352f1/include/boost/spirit/home/classic/phoenix/tuples.hpp#L21  
  
These two definitions cause `phoenix::tuple<>` to refer to two different types in different translation units, which is an ODR violation.  
  
All `PHOENIX_LIMIT` definitions should consistently have the same value.

---

## Comment 1

> Username: eli-schwartz  
> Created at: 2025-05-06 19:34:09 UTC  
> Updated at: 2025-05-06 19:35:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/800#issuecomment-2855724357  

Thanks for tracking down the root cause. I notice that the code comments in both places claim the value is "3" (which is clearly no longer true), but the actual value was changed in one location back in 2010 (without documenting why, but svn was hardly good at commit messages I suppose...): https://github.com/boostorg/spirit/commit/c59544076edc54138d3560980d0290c085fd26f9

---

## Comment 2

> Username: saki7  
> Created at: 2025-05-07 03:41:21 UTC  
> Url: https://github.com/boostorg/spirit/issues/800#issuecomment-2856929795  

As a contributor, I should mention that the classic version is no longer actively maintained and is unlikely to be reviewed further.

---

## Comment 3

> Username: pdimov  
> Created at: 2025-05-07 07:50:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/800#issuecomment-2857474420  

What does this mean in practical terms? That you won't be fixing that? That you expect a PR from me? Or that I need to fix it myself?

---

## Comment 4

> Username: saki7  
> Created at: 2025-05-07 08:49:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/800#issuecomment-2857715315  

Apologies if that came across as offensive. However, I wouldn't expect the bugs related to the "classic" version to be fixed anytime soon. Most of its code is inherited from the 17-year-old SVN codebase, and the current maintainers are primarily focused on X3 and V2. As far as I can tell, there's no clear consensus on who, if anyone, is actively *assigned* for the classic version—and in practice, it appears that none of the maintainers are.  
  
So yes, someone should submit a PR for this; otherwise, this issue may soon go stale.

---

## Comment 5

> Username: saki7  
> Created at: 2025-05-07 09:09:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/800#issuecomment-2857784154  

I can't resist to say that the root cause lies in the deployment of Boost.Quickbook itself. Its reliance on this archaic codebase is a clear reason why we shouldn’t continue using a self-built documentation framework. Sure, it served its purpose in the past—we definitely needed it 15 years ago. But in 2025, it’s just creating more and more issues like this.  
  
I should also admit that I’ve been a long-time reader of the Boost Developers Mailing List. I’m well aware of the recurring discussions around the maintenance burden of the de facto documentation system.  
  
I’m yet another developer who’s struggled with its arcane and unconventional syntax. I remember how stressful it was having to learn QuickBook just to get my PR merged last year. I still remember when the maintainer said that [he doesn't have a working environment to build the docs](https://github.com/boostorg/spirit/pull/788#issuecomment-2292377078). That was genuinely surprising to me.

---

## Comment 6

> Username: pdimov  
> Created at: 2025-05-07 09:23:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/800#issuecomment-2857829804  

We're _slowly_ migrating to Asciidoc. :-)

---

## Comment 7

> Username: pdimov  
> Created at: 2025-05-07 09:53:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/800#issuecomment-2857929628  

The PR is here: https://github.com/boostorg/spirit/pull/801

---

## Comment 8

> Username: pdimov  
> Created at: 2025-05-07 10:34:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/800#issuecomment-2858051684  

> I still remember when the maintainer said that he doesn't have a working environment to build the docs. That was genuinely surprising to me.  
  
What makes this amusing is that Joel is one of the original authors of Quickbook, if I'm not mistaken. :-) (If not the original author.)

---

## Comment 9

> Username: saki7  
> Created at: 2025-05-07 11:10:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/800#issuecomment-2858158334  

Thanks for the PR anyways. That's a decent fix and should be merged. I'd have already hit the big green button if I had the privilege.  
  
@pdimov That said, we really need to do something more constructive than fixing this 17-year-old codebase. Honestly, we should deprecate QuickBook already, and ultimately remove the obsolete docs that belong to obsolete libraries. Broken links? Sure. But why can't we just point the index to the valid html of the older Boost version? That's fairly straightforward and cause no harm.  
  
If we don't, people will just keep waiting for *someone* to fix the docs―and that *someone* inevitably ends up being core devs like you, who are generously spending time patching unmaintained stuff. It's so unproductive and unfair.  
  
I know, this argument is so out of scope. I was trying to post this on the mailing list but I was so disturbed by all those politics and nonsense on the recent governance.

---

## Comment 10

> Username: djowel  
> Created at: 2025-05-07 23:24:08 UTC  
> Url: https://github.com/boostorg/spirit/issues/800#issuecomment-2860683257  

Guilty as charged. I'm so sorry, I do not have enough bandwidth anymore to have active participation in Boost. Thank you very much for the PR, @pdimov. I'll merge it now.

---

## Comment 11

> Username: saki7  
> Created at: 2025-05-08 01:30:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/800#issuecomment-2861150867  

@djowel Would you be willing to support me if I volunteer to maintain Boost.Spirit? If so, I plan to review (eventually) all open issues and PRs, at the very least to determine whether they should be closed as stale or marked with a "needs more info" label. That action needs write permission on the repo. Given the recent situation, I also think it's time to formally deprecate Classic. I'd rather start doing something constructive than just posting rants.

---

## Comment 12

> Username: eli-schwartz  
> Created at: 2025-05-08 01:34:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/800#issuecomment-2861156079  

> If so, I plan to review (eventually) all open issues and PRs, at the very least to determine whether they should be closed as stale or marked with a "needs more info" label. That action needs write permission on the repo.  
  
(Github's permission model supports the triage role, which is sufficient to close or label issues while not handing out full write permission, so that's an option for incrementally onboarding helpful community members.)

---

## Comment 13

> Username: saki7  
> Created at: 2025-05-08 01:39:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/800#issuecomment-2861164869  

> Github's permission model supports the triage role  
  
Thanks for pointing that out, I wasn't aware of that feature. That said, I'm volunteering as a *maintainer* now, not just helping with triage. As I mentioned earlier, a deprecation proposal like this won't be taken seriously by the community unless it comes from a maintainer. I'm seeking endorsement for taking on that responsibility.

---

## Comment 14

> Username: djowel  
> Created at: 2025-05-08 06:17:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/800#issuecomment-2861901117  

> [@djowel](https://github.com/djowel) Would you be willing to support me if I volunteer to maintain Boost.Spirit? If so, I plan to review (eventually) all open issues and PRs, at the very least to determine whether they should be closed as stale or marked with a "needs more info" label. That action needs write permission on the repo. Given the recent situation, I also think it's time to formally deprecate Classic. I'd rather start doing something constructive than just posting rants.  
  
Absolutely!

---

## Comment 15

> Username: djowel  
> Created at: 2025-05-08 06:23:21 UTC  
> Url: https://github.com/boostorg/spirit/issues/800#issuecomment-2861915642  

> > Github's permission model supports the triage role  
>   
> Thanks for pointing that out, I wasn't aware of that feature. That said, I'm volunteering as a _maintainer_ now, not just helping with triage. As I mentioned earlier, a deprecation proposal like this won't be taken seriously by the community unless it comes from a maintainer. I'm seeking endorsement for taking on that responsibility.  
  
I'm open to that. And I truly appreciate your support. Take note that this includes maintainership of the accompanying libraries: Fusion and Phoenix, and possibly Boost.Tuple, which was handed on to me a long time ago (which should also probably be deprecated).

---

## Comment 16

> Username: saki7  
> Created at: 2025-05-08 06:53:42 UTC  
> Url: https://github.com/boostorg/spirit/issues/800#issuecomment-2861979114  

@djowel Thanks so much for your endorsement! I'd like to ask you a few questions about the actual maintenance work—such as how to build consensus around breaking changes, among other things. Since it doesn’t seem appropriate to continue the conversation on a closed issue, would it be alright if I reached out to you directly via Discord, Slack, or email?

---

## Comment 17

> Username: djowel  
> Created at: 2025-05-08 07:01:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/800#issuecomment-2861994521  

> [@djowel](https://github.com/djowel) Thanks so much for your endorsement! I'd like to ask you a few questions about the actual maintenance work—such as how to build consensus around breaking changes, among other things. Since it doesn’t seem appropriate to continue the conversation on a closed issue, would it be alright if I reached out to you directly via Discord, Slack, or email?  
  
Discord or email works for me for private messages, but https://github.com/boostorg/spirit/discussions can also be utilized for public discussions.

---

## Comment 18

> Username: saki7  
> Created at: 2025-05-08 08:16:50 UTC  
> Url: https://github.com/boostorg/spirit/issues/800#issuecomment-2862177724  

@djowel I posted my questions here: https://github.com/boostorg/spirit/discussions/802
