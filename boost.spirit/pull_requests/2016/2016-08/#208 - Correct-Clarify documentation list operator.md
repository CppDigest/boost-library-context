# #208 Correct/Clarify documentation list operator [Closed]

> Username: mxc-commons  
> Created at: 2016-08-19 00:39:31 UTC  
> Updated at: 2016-08-23 03:55:29 UTC  
> Closed at: 2016-08-19 18:04:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/208  

This PR adds a fix to the operator % documentation and adds a note according to that.  
  
I'd still prefer a solution where the list operator does not implicitly omits b, because lit and omit are available. That would appeala more compliant to me.  
  
See discussion below. You recommended to comment that.   
  
https://sourceforge.net/p/spirit/mailman/spirit-general/thread/OF9AB6BCBF.6A515C09-ONC1257FBE.0060CDB1-C1257FBE.0061E33E%40maxence.de/#msg35114909

---

## Comment 1

> Username: mxc-commons  
> Created_at: 2016-08-19 00:49:42 UTC  
> Url: https://github.com/boostorg/spirit/pull/208#issuecomment-240899153  

Sorry for the several commits. At the end one file is changed as intended.  
  
Seems I put bullshit on bullshit. Currently I trying to get control over git branches. Thank you so much for your patience if I deserve it.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2016-08-19 10:03:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/208#issuecomment-240979371  

You can make `git rebase -i HEAD~3` and drop those commits.  
  
And a suggestion: do a `git rebase upstream/develop` instead of `git merge upstream/develop` (if upstream is not configured [here is a manual](https://help.github.com/articles/configuring-a-remote-for-a-fork/)).

---

## Comment 3

> Username: vtnerd  
> Created_at: 2016-08-19 15:45:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/208#issuecomment-241054831  

Someone else brought this up recently on the mailing list (maybe it was you?). The behavior of the list operator should not be changed, it would break too much existing code for little benefit. If the omit was not automatically applied, then the expression `A % B` would expand to `sequence<A, container<sequence<A, B> >` which seems less intuitive than `container<A>`. And then consider that in the majority of cases a single separator is used, and therefore an `omit` would be used anyway.  
  
Further down the exposed attributes are listed on the page, but since this is the second time its come up this documentation could be useful.

---

## Comment 4

> Username: mxc-commons  
> Created_at: 2016-08-19 17:55:18 UTC  
> Updated_at: 2016-08-19 17:57:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/208#issuecomment-241088569  

That was me. :) To read "This is equivalent to a >> *(b >> a)" on the page was all I wanted to know then. I did not continue further down to the attributes list. I later meant to have found a bug when I saw they are not equivalent.  
  
Regarding the operator itself I think it would be more consistent without that implicit commit. I'd personally prefer a modified operator therefore. User's would need to get informed to insert omits.  
  
But: With the proposed slight adjustment of the doc, the behaviour would be unambigously described. That's ok and lowest impact, I think.

---

## Comment 5

> Username: Kojoley  
> Created_at: 2016-08-19 18:03:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/208#issuecomment-241090729  

I remember how I have faced this problem a long time ago, and was confused by documentation too.  
Maybe there exists an alternative solution, so we can control if `b` result in `a % b` should omitted or not? For example it can at least depend on the rule signature.

---

## Comment 6

> Username: mxc-commons  
> Created_at: 2016-08-19 18:17:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/208#issuecomment-241094642  

I re-PR this because of my git desaster. @Kojoley Interesting..

---

## Comment 7

> Username: mxc-commons  
> Created_at: 2016-08-20 02:33:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/208#issuecomment-241173814  

Do you think @Kojoley a prepro switch to control the behaviour of the list operator would be a no-go?

---

## Comment 8

> Username: Kojoley  
> Created_at: 2016-08-21 12:28:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/208#issuecomment-241254647  

I would rather avoid a preprocessor switch.

---

## Comment 9

> Username: mxc-commons  
> Created_at: 2016-08-23 03:55:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/208#issuecomment-241619990  

I think, at least the doc needs correction. a % b is not equivalent to a >> *(b >> a). That's simply not true. I created a PR to fix that documentation issue. I believe that could be accepted. It changes   nothing what was obviously good for everyone the last ten years.  
  
I am new to qi and stumbled over that obviously wrong statement three month ago. Please fix that  at least if you expect and welcome new users. It simply makes things clearer and helps to avoid obsolete discussions.  
  
Again, it feels wrong to me that a % lit('b') is actually equivalent to a % char_('b'). For consistency (my understanding) char_ should in no context be "per se" equivalent to lit. The semantics of char_ or lit should not be implicitely transferred to any operator.  
  
Personally, I do not think, that the operator should behave differently based on the rule's signature. That would be hard to document and it would be even harder to explain.  
  
I also do not like (@Koroley) my suggestion  of controlling the behaviour of the % operator via global define. For me this would have been a way to allow non-consistent behaviour to continue beside a fix.  
  
I know, I am something like a unicorn. Re-discovered c++ five month ago, discovered c++ metaprogramming four month ago, discovered qi three month ago. Since then potentially steeling people's time.  
  
But regardless of what you might think about me, please accept at least  that obvious documentation bug fix. a % b is equivalent to a >>*(b>>a) stole me time. It's a lie.  
  
If qi is feature complete and fix resistent, just say so. Totally acceptable. The argument that "for a decade" nobody cared, is valid. "Don't propose changes to features which are older than three years". Perfectly valid.  
  
Not defining any contributiton rules at all is an invitation to all to contribute, alltough contributiton (change) seems to be not appreciated.  
  
Rgds, Frank  
  
maxence business consulting gmbh  
Frank Hein  
Geschäftsführer  
Am Weißen Stein 1  
41541 Dormagen  
T  +49 2133 - 2599-10  
F  +49 2133 - 2599-29  
M +49 151 - 54742989  
E frank.hein@maxence.de  
  
> Am  21.08.2016 um 14:28 schrieb Nikita Kniazev notifications@github.com:  
>   
> I would rather avoid a preprocessor switch.  
>   
> —  
> You are receiving this because you modified the open/close state.  
> Reply to this email directly, view it on GitHub, or mute the thread.

---
