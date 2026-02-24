# #196 Expect directive for spirit qi 2 [Merged]

> Username: mxc-commons  
> Created at: 2016-07-12 00:50:24 UTC  
> Updated at: 2016-07-16 15:36:50 UTC  
> Merged at: 2016-07-16 15:36:50 UTC  
> Closed at: 2016-07-16 15:36:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/196  

As discussed with Joel de Guzman this PR introduces the expect directive to  
spirit qi 2. PR includes code, test, example and documentation.  
See https://sourceforge.net/p/spirit/mailman/spirit-general/thread/nln5d3%24klg%241%40ger.gmane.org/#msg35208052  
1. Added expect.hpp to home/qi/directives.  
   1a. Added forwarding header qi_expectd.hpp in include.  
2. Renamed expectation parser (a > b) struct from expect to expect_operator  
   to prevent name conflicts (expect directive uses struct expect_directive).  
3. Added expect directive to the common terminals (common_terminals.hpp).  
4. Added expectd.cpp to the test suite.  
5. Added expectd.cpp to the examples.  
6. Added expect directive documentation to directive.qbk. Crosslinked   
   expectation operator and expect directive docs.  
7. Added expect directive to the index.idx file for index-generation.  
8. Added expect directive to the qi quick_reference.qbk.  
9. Added expect directive to the spirit reference card (tex, pdf)  
10. Created a new section in the whats_new.hpp.  
11. Changed spirit version to 2.5.4. Spirit index.html showed 2.5.2  
    allthough an 2.5.3 What's New section already existed. Fixed.

---

## Comment 1

> Username: djowel  
> Created_at: 2016-07-12 22:47:27 UTC  
> Updated_at: 2016-07-15 00:09:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/196#discussion_r70536562  

Why is this named expectd? It does not follow our naming conventions.

---

## Comment 2

> Username: djowel  
> Created_at: 2016-07-12 23:28:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/196#issuecomment-232212279  

BTW, Spirit X3 has the expect directive. It would be best to sync the naming and directory structure and implementation with that. Have you taken a look? So far everything is good, but I want to be sure we have consistency.

---

## Comment 3

> Username: mxc-commons  
> Created_at: 2016-07-13 02:39:40 UTC  
> Updated_at: 2016-07-15 00:09:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/196#discussion_r70557141  

It's called qi_expectd.hpp because there is a qi_expect.hpp in the include directory already.  
The test case is called expectd.cpp because there is an expect.cpp in the test directory already. Both existing files are for the expectation operator. Please advice what I should do about that. My solution was to append a 'd' to mark the 'd'irective.  
  
For me the naming, implementation and directory structure looks quite consistent with qi and X3 also. If you see any issues please provide some more detail. What am I missing?

---

## Comment 4

> Username: djowel  
> Created_at: 2016-07-13 04:04:42 UTC  
> Updated_at: 2016-07-15 00:09:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/196#discussion_r70561908  

How about doing what X3 does?

---

## Comment 5

> Username: mxc-commons  
> Created_at: 2016-07-13 12:57:13 UTC  
> Updated_at: 2016-07-15 00:09:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/196#discussion_r70619252  

X3 seems not to have an expect operator.

---

## Comment 6

> Username: vtnerd  
> Created_at: 2016-07-13 13:17:58 UTC  
> Updated_at: 2016-07-15 00:09:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/196#discussion_r70622219  

The X3 expect operator (`operator>`) is in operator/sequence.hpp

---

## Comment 7

> Username: mxc-commons  
> Created_at: 2016-07-13 13:31:35 UTC  
> Updated_at: 2016-07-15 00:09:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/196#discussion_r70624542  

Oh, I see. Would be all but trivial to refactor the qi expect operator to act like x3's.

---

## Comment 8

> Username: mxc-commons  
> Created_at: 2016-07-14 19:35:33 UTC  
> Updated_at: 2016-07-15 00:09:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/196#discussion_r70869019  

So how to continue?

---

## Comment 9

> Username: djowel  
> Created_at: 2016-07-14 21:22:48 UTC  
> Updated_at: 2016-07-15 00:09:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/196#discussion_r70886729  

Your call. You can probably just add the new commits in this PR, no?

---

## Comment 10

> Username: mxc-commons  
> Created_at: 2016-07-14 22:52:45 UTC  
> Updated_at: 2016-07-15 00:09:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/196#discussion_r70897958  

I could and I would. I actually don't have a clue what you want me to do neither what the problem in particular is.  
  
Is it that a header qi_expectd.hpp in include is not acceptable? We could just remove it. As far as I understand it, the current header structure is in home, and include headers are there for historical reasons forwarding to home. If that was true, i would remove the header and adjust the doc. Is it that what I should do?  
  
Or should I forward include both directive and operator header from qi_expect.hpp? I could do that. This would make qi_expectd obsolete without dropping the forward include.   
I assume this to be the best solution because, so I will start by committing that.  
  
Should I change the expect operators implementation? And how? Should it use the expect directive for the parsing job of the operator? IMO there is no benefit from that which would justify the change. And would introduce a new dependency.  
  
Or is it at last the only the name of the guardian which is not ok?  
  
Can you please point me to the naming conventions you referred to? "The Porting from 1.8" in the docs is the only I found mentioning naming conventions specific to qi. And, of course, the boost conventions which do not go into the detail of the library's internals.

---

## Comment 11

> Username: djowel  
> Created_at: 2016-07-14 22:56:51 UTC  
> Updated_at: 2016-07-15 00:09:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/196#discussion_r70898406  

Your "best solution" sounds good. There's actually no formal naming convention. Just follow what we have in the current code, e.g. no abbreviations such as expectd.

---

## Comment 12

> Username: mxc-commons  
> Created_at: 2016-07-14 23:34:46 UTC  
> Updated_at: 2016-07-15 00:09:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/196#discussion_r70901880  

Comitted already. :)

---

## Comment 13

> Username: djowel  
> Created_at: 2016-07-14 23:54:09 UTC  
> Updated_at: 2016-07-15 00:09:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/196#discussion_r70903520  

Ooops. Will this work with C++03? Have we checked if the additions work with C++03? That's our minimum for Qi and Karma (not so for X3).

---

## Comment 14

> Username: djowel  
> Created_at: 2016-07-14 23:54:58 UTC  
> Updated_at: 2016-07-14 23:55:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/196#issuecomment-232825462  

Oh, please test against C++03. I might have spotted some C++11-isms. Make sure all tests pass.

---

## Comment 15

> Username: mxc-commons  
> Created_at: 2016-07-15 00:53:19 UTC  
> Url: https://github.com/boostorg/spirit/pull/196#discussion_r70907887  

I removed the type aliases. Sorry for that.

---

## Comment 16

> Username: djowel  
> Created_at: 2016-07-15 23:01:11 UTC  
> Url: https://github.com/boostorg/spirit/pull/196#issuecomment-233088691  

Great! Are we good to go? Everything tested on C++03? Please confirm so I can merge.

---

## Comment 17

> Username: mxc-commons  
> Created_at: 2016-07-16 13:07:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/196#issuecomment-233129562  

Tested ok against MSVC++ 2006, 2008, 2013 and 2015.

---
