# #40 Support for  multiple long names - PR for initial review only [Closed]

> Username: eyalroz  
> Created at: 2018-02-10 20:12:25 UTC  
> Updated at: 2018-05-01 11:37:07 UTC  
> Closed at: 2018-03-25 19:33:24 UTC  
> Url: https://github.com/boostorg/program_options/pull/40  

Hello @vprus , @pdimov ,  
  
A year and a half ago I wrote you, @vprus , about my interest in implementing some additional features for program_options. You referred me to the boost mailing list, where I had an exchange with Klemens Morgenstern.  
  
... a long while later, I've gotten around to starting to work on that. Now, the list of all of the features I'm interested in is [here](https://github.com/eyalroz/program_options/issues?q=is%3Aopen+is%3Aissue+label%3Aenhancement), but this PR is about a single feature:  
  
[Support multiple alternative long names for some option](https://github.com/eyalroz/program_options/issues/1).  
  
It has another commit, though, unrelated to the feature which is just about avoiding a warning.  
  
I'm asking for a code review of what I've written, to see if it's generally ok with you guys and for some feedback. If you give me the go-ahead I'll add some test cases and make a second PR for you to consider for actual merging.

---

## Comment 1

> Username: jeking3  
> Created_at: 2018-03-06 14:20:28 UTC  
> Url: https://github.com/boostorg/program_options/pull/40#issuecomment-370795965  

I do not see any tests that exercise the new branches in a meaningful way.

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-03-06 14:21:14 UTC  
> Url: https://github.com/boostorg/program_options/pull/40#issuecomment-370796198  

This PR includes a similar fix found in #37.

---

## Comment 3

> Username: eyalroz  
> Created_at: 2018-03-06 15:28:49 UTC  
> Url: https://github.com/boostorg/program_options/pull/40#issuecomment-370818966  

@jeking3 : About the fix in #37 - Yes, that's true. I had just fixed it on my fork not noticing other PRs/issues to get rid of the warning. It's not related to the feature itself and if-and-when the feature is deemed fit for merging I can either drop that fix or branch off a fixed version.  
  
About the lack of tests - yes, I have not written tests yet because I first wanted an initial feedback on my approach; ergo this is not a PR to be actually merged. If you (or you and Vladimir? I'm not sure how it works organizationally) like the approach, I will (hopefully) finalize things based on your feedback, write tests, and create PR which would hopefully be ready to merge.

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-03-06 15:35:32 UTC  
> Url: https://github.com/boostorg/program_options/pull/40#issuecomment-370821398  

I maintain other boost libraries but when I saw your email to the mailing list I thought I would look over the backlog here.  What exactly is the use case requiring multiple long names on an option?

---

## Comment 5

> Username: pdimov  
> Created_at: 2018-03-06 15:37:47 UTC  
> Url: https://github.com/boostorg/program_options/pull/40#issuecomment-370822226  

This is @vprus 's call. Let's hope that he'll find the time to take a quick look at it.

---

## Comment 6

> Username: eyalroz  
> Created_at: 2018-03-06 16:00:39 UTC  
> Updated_at: 2018-03-06 16:01:39 UTC  
> Url: https://github.com/boostorg/program_options/pull/40#issuecomment-370830558  

@jeking3 :   
First - it's not about absolute requirement, it's a convenience feature. After all, you could theoretically only have one long-form option and no short option; nothing absolutely requires short options.  
  
To answer the question though, there are two use cases/aspects of motivation and both of them involve applications with lots and lots of options:  
  
1. Possibility of having shorthands (e.g. acronyms, but not just) for annoyingly-long options. Example: `--frobnicate-special-bars, --fsb`.   
    Q: "Why not just use a single-character shorthand?"  
    A: Not enough single characters; also, difficult to remember if `-f` is for frobincating bars or for, say, `--foo-gets-priority`.  
    Q: "Why not only use `--fsb`"?  
    A: That means requiring someone to always remember the translation back to the long form when looking a command line somebody else has written; also, it makes it unclear what the acronym stands for in the literal sense  
2. Multiple ways of expressing the same thing, so that the user doesn't always have to remember exactly what phrase the developer chose: "Is it `--frobnicate-special-bars` or `--special-bar-frobnication`?" better to just support both.

---

## Comment 7

> Username: eyalroz  
> Created_at: 2018-03-06 16:04:33 UTC  
> Updated_at: 2018-03-06 16:05:01 UTC  
> Url: https://github.com/boostorg/program_options/pull/40#issuecomment-370832050  

@pdimov : Like I said on the mailing list - I don't want to be disrespectful, but it's a problem if library maintenance is entirely up to one person, on the one hand, but he takes than 4 months to respond (or perhaps over a year and half to consider the oldest PR here), on the other hand.

---

## Comment 8

> Username: jeking3  
> Created_at: 2018-03-06 16:14:40 UTC  
> Url: https://github.com/boostorg/program_options/pull/40#issuecomment-370835643  

Sounds like a reasonable use case to me, thanks for the explanation.  You may want to move that into your PR description ( and add tests :) ).

---

## Comment 9

> Username: pdimov  
> Created_at: 2018-03-06 16:47:20 UTC  
> Url: https://github.com/boostorg/program_options/pull/40#issuecomment-370847051  

It is a problem, which is why I said I hoped that he'll chime in, so that we no longer have a problem. :-)  
  
If not, having tests and documentation would be an absolute prerequisite for someone other than the maintainer to merge this.

---

## Comment 10

> Username: vprus  
> Created_at: 2018-03-06 19:28:58 UTC  
> Url: https://github.com/boostorg/program_options/pull/40#issuecomment-370898926  

Thanks for the patch! I guess I am missing some bigger context and maybe  
some ML discussions, but the immediate comment about this change is this:  
while it seems nice-to-have, and the patch does not change many interfaces,  
how many other users also want this feature? I don't recall similar  
requests, but maybe I am wrong  
  
On Tue, 6 Mar 2018, 19:47 Peter Dimov, <notifications@github.com> wrote:  
  
> It is a problem, which is why I said I hoped that he'll chime in, so that  
> we no longer have a problem. :-)  
>  
> If not, having tests and documentation would be an absolute prerequisite  
> for someone other than the maintainer to merge this.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/program_options/pull/40#issuecomment-370847051>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AANye9cKrEy0wnopm1heNT-2vbef9xIrks5tbr2YgaJpZM4SBECR>  
> .  
>  
--   
Vladimir Prus  
http://vladimirprus.com

---

## Comment 11

> Username: eyalroz  
> Created_at: 2018-03-06 19:56:59 UTC  
> Updated_at: 2018-05-01 11:37:07 UTC  
> Url: https://github.com/boostorg/program_options/pull/40#issuecomment-370907914  

Hello @vprus  and thanks for chiming in :-)  
  
To address your points:  
  
* There was a short mailing list discussion in 2016 about this and several other feature proposals I had (thread archived [here](https://lists.boost.org/Archives/boost/2016/08/230525.php)). The bottom line was me being told:  
  
   >  You can just fork the library on github, implement the features and create a pull request there.   
* I don't really have a "big context". I've been using `program_option` in an app of mine, and I since I want to support multiple long options, I need to parse them after-the-fact after collecting the unparsed options. And there are some more features I've been thinking about, but this is perhaps the simplest and most obviously useful.  
* As for "how many users" - I've not done any user/developer surveys about this. If it had been some sort of exotic feature, I would understand you expecting some public "demand"; but in this case - it's basically a a missing feature. Many applications have multiple long-option-names.  
   Take gcc for example: You have` -traditional` and `-traditional-cpp`; `-Wpedantic` and `-pedantic` and so on. Or `X` (the display server binary) which has `-bg` and `-background-color`. If their developers wanted to switch to using `program_option`, they would want this feature.

---

## Comment 12

> Username: eyalroz  
> Created_at: 2018-03-18 18:27:50 UTC  
> Url: https://github.com/boostorg/program_options/pull/40#issuecomment-374028655  

@vprus : You're ignoring this thread again...   
  
Also - are you ok with the approach technically, irrespective of the question of the interest in accepting the PR? If so I'll write tests for it, if not I could conceivably change/fix certain things.

---

## Review 13 [Commented]

> Username: vprus  
> Created_at: 2018-03-18 19:27:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/program_options/pull/40#pullrequestreview-104808350  

📁 src/options_description.cpp

```diff
 132 |+         if (!m_long_names.empty()) {
 133 |+             const std::string& first_long_name = *m_long_names.begin();
 134 |+             if (first_long_name.find('*') != string::npos)
```

> Username: vprus  
> Created_at: 2018-03-18 19:27:22 UTC  
> Updated_at: 2018-03-25 14:42:01 UTC  
> Url: https://github.com/boostorg/program_options/pull/40#discussion_r175300611  

I am not sure this logic is correct. If we have 3 longs options, with the second one matching the input and having "*" at the end, we want to return the actual matched token? This logic will return the matched token only if the first option has trailing "*"

> Username: eyalroz  
> Created_at: 2018-03-18 22:36:40 UTC  
> Updated_at: 2018-03-25 14:42:01 UTC  
> Url: https://github.com/boostorg/program_options/pull/40#discussion_r175307178  

I don't quite get your example. What do you mean by   
  
> "" at the end  
  
? End of what, and what's at that end?  
  
Also, remember that this is `key()`, not `match()`; in `match()` we're obviously more lenient.

> Username: eyalroz  
> Created_at: 2018-03-18 22:45:33 UTC  
> Updated_at: 2018-03-25 14:42:01 UTC  
> Url: https://github.com/boostorg/program_options/pull/40#discussion_r175307549  

By the way... is program_options supposed to support an option having an empty long name and no short name? I don't see code in the current `set_name()` which specifically rules this out (e.g. an `assert()`).

> Username: vprus  
> Created_at: 2018-03-22 18:53:38 UTC  
> Updated_at: 2018-03-25 14:42:01 UTC  
> Url: https://github.com/boostorg/program_options/pull/40#discussion_r176535353  

This code handles a case where long option name has an asterisk at the end (looks like github helpfully removed those asterisks in my original comment. Say, we have long name of `foo*`, it will match either `foo1` or `foo2`, and the `key` method will notice his case and use `foo1` or `foo2` respectively, not `foo*`. As far as I can see, with your patch, given `bar,foo*` as long options, and `foo1` as token, the method will return `bar`, not `foo1`.

> Username: vprus  
> Created_at: 2018-03-22 18:54:30 UTC  
> Updated_at: 2018-03-25 14:42:01 UTC  
> Url: https://github.com/boostorg/program_options/pull/40#discussion_r176535608  

Empty long name and no short name is clearly not a useful case, so a diagnostic might be helpful.


---

## Comment 14

> Username: vprus  
> Created_at: 2018-03-18 19:30:03 UTC  
> Url: https://github.com/boostorg/program_options/pull/40#issuecomment-374037477  

@eyalroz: I am sorry that my response time is not as good as you (or I, even) would have liked. That said, it's quite common situation in open-source, so please don't take it personally. I had quite a number of patches to other projects that took several pings to get any response.  
  
Speaking of demand/motivation - I am asking because (i) you're the first one to request this functionality (ii) while there are existing tools that have multiple spellings, it's not clear than any new tool would want to do that. In case of gcc, at https://gcc.gnu.org/wiki/DiagnosticsGuidelines they say "-pedantic is an obsolete alias for -Wpedantic" (iii) your earlier examples had fictional option names, so it's hard to judge. In general, if I were creating a CLI today, I would prefer to use a single option name for each option, as aliases might save typing but increase confusion.  
  
As for technical approach, it seems like it should not break existing usage, as long option names with commas would be quite rather. On the other hand, it seems that the default behaviour of option_descriptions formatting would be to show only the first long option, so one easily create CLI where accepted options are not listed in the --help output?

---

## Comment 15

> Username: eyalroz  
> Created_at: 2018-03-18 23:20:41 UTC  
> Updated_at: 2018-03-22 21:59:58 UTC  
> Url: https://github.com/boostorg/program_options/pull/40#issuecomment-374065395  

So, this pull request is now stale (I think) as I've rebased my fork's "develop" branch.  Also, I will have at least some work to do following @vprus 's comments and observations of mine following his comments.  
  
But - the general discussion about the merit of this feature is not resolved, so let's continue it here.  
  
* **Reply timing/pings/delays**: it's all good by me.... I was just noticing you've been relatively active with this repository over the past couple of weeks. If you'd like to tell me "never ping me more frequently than once in X weeks" I can oblige.  
* **Demand for the feature**: From looking at the Issues page, you get very very few feature requests. The last one before mine was in 2016. I haven't seen requests on the Boost mailing lists, either. So you could probably say the same about any request at all. I've also mentioned that there don't seem to be statistics or survey data collected about the user base for `program_options`, nor the capacity to conduct such a survey; so it's essentially impossible to tell how many people would like to have this feature, or would have liked to have it in retrospect. In lieu of that we have the mailing list discussion.   
  
   Would you feel more confident if we brought this up on the mailing list again, to get some "I approve / I disapprove" responses about this feature? I don't mind, but I think its balance of merit vs intrusiveness is so skewed that it's a waste of people's time.  
  
* **Long options containing commas**: I don't understand your sentence:  
  >  it seems like it should not break existing usage, as long option names with commas would be quite rather.  
  
  The existing code does not support long option names with commas right now, as the name string is broken up with a `find()` rather than an `rfind()`. So the feature doesn't interfere with valid use of the library.  
* **Show only first option in description?**: Indeed, the way I've coded this feature, only the first option will be shown by `os << some_options_description`. This intentional choice was the simplest and required no considerations of  printing style, wrapping, lengths and so on. I don't have a strong opinion on this choice I've made.  
  
   Note, though, that if a different choice is to be considered, we'd need to think about whether the user wants/needs to see all the options or not, or maybe sometimes s/he wants them all and sometimes not. We'd also need to think about what formatting is appropriate. If we stick with only one option name being printed, I suppose that at the very least some kind of comment and/or documentation note should indicate that the first long name is the canonical name that gets printed in the usage information.  
  
* **The GCC examples**: There are more than just `-pedantic` and `-Wpedantic`, I gave another one and you can find more aliases.   
  
* **The pros and cons of multiple option names**: You've focused on a pair of considerations: Saving typing vs clarity/avoiding confusion.   The saving of typing is actually most relevant to the availability of one-letter switches; they save the most typing but are the easiest to confuse, especially since they're specified differently (i.e. `-rnone` vs `--reuse=none`). Once you have that, it doesn't seem more confusing to have a third or a fourth alias. So following your argument, you should just remove the short option and have everything very clear-cut....  
  
   Also, saving typing is just one benefit of multiple long names, and probably not the most significant. The most significant IMO is the ability to type in the long option without having to remember the minutiae. If your program only has 5, 10, 20 options, then fine; but if it has 100 or 200 you'll always forget the exact naming of _some_ of the options - even if you've just looked. It helps when you can type in any of `--data-all-ones`, `--all-ones-data`, `--use-one-for-data`, `--use-1-for-data`, `--one-for-data`, `--fill-data-with-one`, `--fill-data-with-ones`, `--use-one-as-data`. This example also has bearing on whether all options should always gets printed.

---

## Comment 16

> Username: vprus  
> Created_at: 2018-03-24 11:00:09 UTC  
> Url: https://github.com/boostorg/program_options/pull/40#issuecomment-375871944  

Thanks for extended explanation! I still believe that ideally, CLI should have just one naming for any option, but then, I appreciate your argument that having one canonical option, and one abbreviation might be better than having one canonical option and a one-letter abbreviation. Therefore, I'll be find with applying this PR when its technically ready.  
  
One specific point, regarding long options containing commas - what I meant to say, is that indeed, your patch is unlikely to break any existing usage.

---

## Comment 17

> Username: codecov[bot]  
> Created_at: 2018-03-25 14:42:03 UTC  
> Url: https://github.com/boostorg/program_options/pull/40#issuecomment-375975610  

# [Codecov](https://codecov.io/gh/boostorg/program_options/pull/40?src=pr&el=h1) Report  
> Merging [#40](https://codecov.io/gh/boostorg/program_options/pull/40?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/program_options/commit/491cb17e3abd7f006ac343eb7789fcd8e141751a?src=pr&el=desc) will **decrease** coverage by `0.58%`.  
> The diff coverage is `26.53%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/program_options/pull/40/graphs/tree.svg?height=150&width=650&token=UMdM0EgHK3&src=pr)](https://codecov.io/gh/boostorg/program_options/pull/40?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #40      +/-   ##  
===========================================  
- Coverage    50.29%   49.71%   -0.59%       
===========================================  
  Files           23       23                
  Lines         1372     1386      +14       
  Branches       694      708      +14       
===========================================  
- Hits           690      689       -1       
- Misses         110      113       +3       
- Partials       572      584      +12  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/program_options/pull/40?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [...lude/boost/program\_options/options\_description.hpp](https://codecov.io/gh/boostorg/program_options/pull/40/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9ncmFtX29wdGlvbnMvb3B0aW9uc19kZXNjcmlwdGlvbi5ocHA=) | `0% <ø> (ø)` | :arrow_up: |  
| [src/options\_description.cpp](https://codecov.io/gh/boostorg/program_options/pull/40/diff?src=pr&el=tree#diff-c3JjL29wdGlvbnNfZGVzY3JpcHRpb24uY3Bw) | `47.69% <26.53%> (-3.13%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/program_options/pull/40?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/program_options/pull/40?src=pr&el=footer). Last update [491cb17...dd17186](https://codecov.io/gh/boostorg/program_options/pull/40?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 18

> Username: eyalroz  
> Created_at: 2018-03-25 19:33:24 UTC  
> Url: https://github.com/boostorg/program_options/pull/40#issuecomment-375996772  

Ok, I think we're done with the discussion for now. So - I'm closing this, and will open another PR - hopefully very soon - after I've addressed the (minor) technical points, and added tests.

---
