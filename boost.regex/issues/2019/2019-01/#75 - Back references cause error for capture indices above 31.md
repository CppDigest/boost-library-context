# #75 - Back references cause error for capture indices above 31 [Closed]

> Username: JohnAlt2  
> Created at: 2019-01-09 17:26:37 UTC  
> Updated at: 2020-08-21 10:30:03 UTC  
> Closed at: 2020-01-21 10:13:41 UTC  
> Url: https://github.com/boostorg/regex/issues/75  

Despite Boost library documentation showing much higher back reference index values (1,000+) it looks like there is a regex syntax error returned when a back reference is to a 32nd or more capture group within the expression. (I do not have access to see the underlying calling code nor actual error returned)  
  
Boost documentation:-  
\g{1}	Match whatever matched sub-expression 1: this form allows for safer parsing of the expression in cases like \g{1}2 or for indexes higher than 9 as in \g{1234}  
  
It is not the number of group captures nor of back references that causes a problem as such. It is a failure to load a regular expression when any back reference is configured to access a group capture with an index of more than 31. This occurs both for absolute index values and relative position references. The error occurs before the regular expression gets to run a scan.   
  
Occurs in our integration with Boost Regex 1.64.0 (and 1.56.0)  
  
Can be demonstrated in "BRegex test" tool. It does not show any error but does fail to detect the text:- https://code.google.com/archive/p/bregextest/downloads  
  
Test with an input text of "WORKING"  
  
### Crash examples ###  
  
Having 32 group captures and "-1" relative back reference causes a crash  
"(?:(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)\g{-1}|WORKING)"  
or  
"(?:(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)\g-1|WORKING)"  
  
Same again in minimal form without the test aid parts  
"(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)\g{-1}"  
  
40 group captures with a "-9" relative back reference (i.e. referencing the 31st capture) crashes  
"(?:(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)\g{-9}|WORKING)"  
		  
40 group captures with a absolute back reference to the 32nd capture crashes  
"(?:(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)\g{32}|WORKING)"  
or  
"(?:(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)\g32|WORKING)"  
  
		  
### Working examples ###  
  
Identical to 1st crash example other than having only 31 group captures  
"(?:(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)\g{-1}|WORKING)"  
		  
Identical to 1st crash example other than having no back-reference i.e. still has the 32 group captures  
"(?:(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)|WORKING)"  
  
40 or much more group captures still works so long as there is no relative "-1" relative back reference  
"(?:(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)|WORKING)"  
		  
40 group captures with a "-10" relative back reference (i.e. referencing the 30th capture) works  
"(?:(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)\g{-10}|WORKING)"  
		  
40 group captures with a absolute back reference to the 31st capture works  
"(?:(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)(z)\g{31}|WORKING)"

---

## Comment 1

> Username: rdoeffinger  
> Created at: 2019-11-16 18:05:30 UTC  
> Url: https://github.com/boostorg/regex/issues/75#issuecomment-554660330  

I don't see why it would crash - are you sure that it doesn't just raise an exception?  
The limitation to 32 backrefs is by design and as far as I can tell at least somewhat intentional:  
unsigned                      m_backrefs;           // bitmask of permitted backrefs  
Increasing it to 64 should be relatively simple and cheap, if it was considered sufficiently valuable, but beyond would be a bit of a problem.  
The documentation I believe was only meaning to explain the syntax and not imply that 1234 would actually be a valid backref.  
But I didn't see the limitation to 32 documented anywhere admittedly.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-11-16 19:44:45 UTC  
> Url: https://github.com/boostorg/regex/issues/75#issuecomment-554668147  

I will get to this and the other regex issues shortly (honestly!).

---

## Comment 3

> Username: rdoeffinger  
> Created at: 2019-11-16 23:20:00 UTC  
> Url: https://github.com/boostorg/regex/issues/75#issuecomment-554683773  

Great to hear someone's "around"!  
Don't get worried about my pull requests though, that's all just "it's easy to improve so I really should submit it" stuff and not critical.  
Hopefully without making your efforts feel less appreciated I also have to add:  
I realized that our requirements have moved enough that we'd better use std::regex anyway, which might be relevant to others as well; and maybe it might be time for the documentation to have a short section on when one might want to prefer one over the other concerning std::regex vs. boost::regex?

---

## Comment 4

> Username: JohnAlt2  
> Created at: 2019-11-18 10:50:56 UTC  
> Updated at: 2019-11-18 10:58:06 UTC  
> Url: https://github.com/boostorg/regex/issues/75#issuecomment-554962304  

Great to here this is being looked at, thanks.  
In terms of documentation, no matter what was meant, when the documentation says "as in \g{1234}" that is an explicit statement that "\g{1234}" is valid. Though not even we would need a 4 digit index.  
Re crash or exception: Personally I do not know as I am not a software developer, I author the data (i.e. regular expressions) that our software uses and from my selfish point of view there is no difference ;¬) as Boost Regex still does not operate as expected or documented. I could ask a software developer to investigate if that does provide any meaningful information but the same limitation also exists in the free 3rd party tool "Boost Regex Tester".  
**Use case background:**   
We create comprehensive therefore complex search strings that are often combined together in to massive single expressions due to logic functionality and/or optimisation reasons. A single compilation expression is potentially run on tens of thousands of user devices internationally.  
**Technical requirement:**  
Specifically there is a need for frequent "(A|B|C)somethingbetween\g{-1}somethingelse\g{-1}", this single relative back-reference operator is one of the key attractions of Boost Regex library as it allows usage of back references in an environment of modular concatenated expressions where there is no idea what index value a capture will be in the final compilation of regexes.  
Technically (and being selfish again) for our distributed expressions we don't even need 9 "\g{_n_}" captures indexed. We just need "\g{-1}" to always be valid. e.g. as a parallel push stack, that would completely remove absolute index limit from relative back references.  
Increase from 31 to 63 limit would still mean that at any point in the future a concatenation might occur that would produce failed expressions as it is not even a two fold increase on the number of captures were generating (40+) when we encountered this issue.  
**Limitation impact:**  
We have had to reduce the accuracy of existing detections, or make even longer and more complex expression (greater maintenance error risks). This reduces the level of security provided to our users.  
I hope this context helps.

---

## Comment 5

> Username: JohnAlt2  
> Created at: 2020-01-10 16:11:14 UTC  
> Updated at: 2020-01-14 11:55:03 UTC  
> Url: https://github.com/boostorg/regex/issues/75#issuecomment-573099088  

Is any fix likely for this in the upcoming months?  
"\g{-1}" not being reliable in large expressions is causing us more and more pain as the scope of our regex data in our product grows. Personally I do not care about any limit to "\g{n}" even if much smaller than documented. But "\g{-1}" needs to always work for us.  
  
Note#1: Looks like it is a handled exception, reported as a syntax error typically reported at the offset of the first  "\g{-1}" that occurs after thirty-ish capture groups.    
Note#2: There is some variability as to when this does or does not happen with between 1-64-0 and 1-56-0. And in 1-64-0 there seems some OS platform related variation (raised in https://github.com/boostorg/regex/issues/96).  
@rdoeffinger  our requirements have stayed the same and Boot::Regex is a firm choice for us as we very much use elements of it that separate it from other libraries. It is the scale of our usage that has changed and therefore hit a limit that documentation very strongly implied would not exist.

---

## Comment 6

> Username: JohnAlt2  
> Created at: 2020-01-14 12:05:51 UTC  
> Url: https://github.com/boostorg/regex/issues/75#issuecomment-574143210  

Testing to try and find a work-around showed that the 31 captures limit also applies to named captures. A syntax error occurs if an expression has 32 or more differently named expressions. Until recently named captures and un-named captures were two separate limits, allowing 62 capture results between the two types. But recently these have become a single limit of 31 on all captures (see https://github.com/boostorg/regex/issues/96).

---

## Comment 7

> Username: JohnAlt2  
> Created at: 2020-01-14 12:13:20 UTC  
> Url: https://github.com/boostorg/regex/issues/75#issuecomment-574145900  

QUESTION: Is this just a validation limit or is it a real code limit?  
i.e. Is it a case that the expression pre-validation is rejecting when the actual regex engine would be fine with higher number of capture groups; or is there a functional limit in what the engine can currently cope with?

---

## Comment 8

> Username: jzmaddock  
> Created at: 2020-01-14 13:31:42 UTC  
> Url: https://github.com/boostorg/regex/issues/75#issuecomment-574174926  

My apologies for being AWOL, just backed up with other stuff, I'm going to try and look at this next.

---

## Comment 9

> Username: jzmaddock  
> Created at: 2020-01-16 20:26:03 UTC  
> Url: https://github.com/boostorg/regex/issues/75#issuecomment-575331278  

There are currently two hard limits here:  
  
1) The highest index you can define a backref to (it doesn't matter whether you have expressed it as a relative backref or not, since internally only absolute indexes are used).  This is currently the same as the number of bits in an unsigned less one.  
2) Back references to indexes greater than 1000 have a special meaning - they're hash values for named sub-expressions.  
  
I can increase (1) fairly easily to some new fixed value, (2) is a bit harder.  
  
BTW these limits are all in the regex parser phase, not in the execution as such.  
  
What I'm less sure about is whether to use a new/higher fixed limit, or use a dynamic set of indexes and effectively remove the limit.  The catch with the latter is that it requires slow/dynamic memory allocation right from the start instead of fast/simple bitmap access.

---

## Comment 10

> Username: JohnAlt2  
> Created at: 2020-01-17 09:50:57 UTC  
> Url: https://github.com/boostorg/regex/issues/75#issuecomment-575554571  

Re fixed limit option: This would allow an increase to 511 capture groups i.e. a 16 fold increase which (for our own selfish needs) should be good for several years expression expansion at the very least.  
  
Re dynamic option: Again speaking selfishly, a redesign rather than a simple tweak would risk delaying a fix release. In regards to performance impact, that obviously depends on magnitude of impact <20% bearable; 50%> unbearable.  But an increase to 511 captures would be a full solution as far as I am concerned.  
  
If the recent additional restriction could be undone as well even better (https://github.com/boostorg/regex/issues/96) but that is not important if the limit here can be increased four-fold or more.  
(BTW: Thanks very much for looking at this)

---

## Comment 11

> Username: JohnAlt2  
> Created at: 2020-04-28 11:02:18 UTC  
> Url: https://github.com/boostorg/regex/issues/75#issuecomment-620536293  

@jzmaddock   
Any idea if this will be included in Boost 1.73.0 release?   
There seems to be no sign of a 'Regex' library update in the Beta 1 content.   
Regards

---

## Comment 12

> Username: jzmaddock  
> Created at: 2020-04-28 16:43:55 UTC  
> Url: https://github.com/boostorg/regex/issues/75#issuecomment-620724343  

Oh :(  
  
My bad I'm afraid I forgot to merge to master and the release is out now :(  
  
I've just done the merge so it definitely will be in 1.74.  
  
My sincere apologies, badly dropped the ball there!

---

## Comment 13

> Username: JohnAlt2  
> Created at: 2020-08-18 16:01:55 UTC  
> Url: https://github.com/boostorg/regex/issues/75#issuecomment-675567497  

@jzmaddock   
Looking at the release notes and documentation for 1.74 there is no mention of any update of the regex module - again?  
https://www.boost.org/users/history/version_1_74_0.html  
https://www.boost.org/doc/libs/1_74_0/

---

## Comment 14

> Username: jzmaddock  
> Created at: 2020-08-21 10:30:03 UTC  
> Url: https://github.com/boostorg/regex/issues/75#issuecomment-678201404  

Ah, my bad (again!), those fixes are in the release, I just haven't updated the release notes.
