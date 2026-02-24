# #182 Using BOOST TTI for Type Traits Introspection [Closed]

> Username: octopus-prime  
> Created at: 2016-03-28 16:24:01 UTC  
> Updated at: 2016-03-30 22:44:27 UTC  
> Closed at: 2016-03-30 22:44:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/182  

Replaced hand written Type Traits Introspection code by using BOOST TTI library.

---

## Comment 1

> Username: octopus-prime  
> Created_at: 2016-03-28 19:25:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#issuecomment-202544623  

Currently there is no usage of is_container in container_traits...  
Perhaps we should use it as guards for the traits using std::enable_if ?!

---

## Comment 2

> Username: djowel  
> Created_at: 2016-03-28 22:24:12 UTC  
> Updated_at: 2016-03-30 22:21:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#discussion_r57643953  

Prefer std instead of Boost, unless there's some performance benefit. In this case, it might be the other way around. MPL has some extra scaffoldings that add to the total compile time.   
  
On second thought, use of mpl::bool is pervasive in x3 anyway, so it's probably better your way.

---

## Comment 3

> Username: octopus-prime  
> Created_at: 2016-03-29 17:54:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#issuecomment-203025877  

Okay. We could try to use std::integral_constant<bool,...> instead of boost::mpl::bool_

---

## Comment 4

> Username: djowel  
> Created_at: 2016-03-29 21:41:21 UTC  
> Updated_at: 2016-03-30 22:21:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#discussion_r57805909  

Is std::integral_constant needed here? isn't the type of detail::has_member_function_reserve a std::true_type or false_type already?

---

## Comment 5

> Username: djowel  
> Created_at: 2016-03-29 21:41:41 UTC  
> Updated_at: 2016-03-30 22:21:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#discussion_r57805940  

same comment

---

## Comment 6

> Username: octopus-prime  
> Created_at: 2016-03-29 21:49:48 UTC  
> Updated_at: 2016-03-30 22:21:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#discussion_r57807031  

The documentation says BOOST TTI uses boost::mpl::bool_ too ...   
The type you wanted me to remove in our code

---

## Comment 7

> Username: djowel  
> Created_at: 2016-03-29 21:52:43 UTC  
> Updated_at: 2016-03-30 22:21:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#discussion_r57807387  

So that defeats the purpose and it's better to keep the mpl::bool in that case. Actually, I had second thoughts on that (edited the comment).

---

## Comment 8

> Username: octopus-prime  
> Created_at: 2016-03-29 22:20:10 UTC  
> Updated_at: 2016-03-30 22:21:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#discussion_r57810793  

Okay. I will revert to boost::mpl::bool_ tomorrow.  
Do you like the "using xxx" style? Or do you want "struct xxx : mpl:bool_" again?

---

## Comment 9

> Username: djowel  
> Created_at: 2016-03-29 22:21:44 UTC  
> Updated_at: 2016-03-30 22:21:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#discussion_r57810987  

using xxx looks good!

---

## Comment 10

> Username: octopus-prime  
> Created_at: 2016-03-30 21:49:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#issuecomment-203649927  

Using mpl::bool_ again.

---

## Comment 11

> Username: djowel  
> Created_at: 2016-03-30 21:52:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#issuecomment-203651687  

One thing: I've been cleaning up test/x3/Jamfile. Please sync up.

---

## Comment 12

> Username: djowel  
> Created_at: 2016-03-30 21:53:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#issuecomment-203652138  

Also, don't hesitate to add your name, esp. on files that you created and on files that you significantly contributed in.

---

## Comment 13

> Username: octopus-prime  
> Created_at: 2016-03-30 21:56:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#issuecomment-203654161  

Sorry! eclipse created the name automatically and folded the comment ;-)

---

## Comment 14

> Username: octopus-prime  
> Created_at: 2016-03-30 22:00:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#issuecomment-203657088  

I merged 1h ago. I think my fork is up-to-date. And github says "This branch has no conflicts with the base branch".

---

## Comment 15

> Username: djowel  
> Created_at: 2016-03-30 22:06:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#issuecomment-203659742  

Odd, I cleaned things up and rearranged a bit and added a test x3_variant.cpp. Now my changes have been reverted. Please see test/x3/Jamfile: https://github.com/boostorg/spirit/blob/develop/test/x3/Jamfile

---

## Comment 16

> Username: octopus-prime  
> Created_at: 2016-03-30 22:16:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#issuecomment-203663628  

Is https://github.com/boostorg/spirit/blob/develop/test/x3/Jamfile the correct jamfile? My jamfile has a lot of out-commented test...

---

## Comment 17

> Username: octopus-prime  
> Created_at: 2016-03-30 22:22:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#issuecomment-203664896  

Anyhow... Replaced test/x3/Jamfile by your linked Jamfile.

---

## Comment 18

> Username: djowel  
> Created_at: 2016-03-30 22:24:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#issuecomment-203665430  

That is the latest Jamfile from the develop branch. You'll need to take care that you are not overwriting some new changes like that one. I might not be able to spot everything. Have you figured out why that happened?

---

## Comment 19

> Username: djowel  
> Created_at: 2016-03-30 22:25:47 UTC  
> Updated_at: 2016-03-30 22:26:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#issuecomment-203665675  

Also, make sure all tests pass on every PR. The tests would've failed due to incorrect directory references.

---

## Comment 20

> Username: djowel  
> Created_at: 2016-03-30 22:28:04 UTC  
> Updated_at: 2016-03-30 22:28:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#issuecomment-203666735  

Whoops, no. This PR also reverted my directory structure changes. I hoisted some files up one level, now it's back to where they were before my changes. Please investigate what happened.

---

## Comment 21

> Username: octopus-prime  
> Created_at: 2016-03-30 22:28:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#issuecomment-203666984  

No. I don't know :-(  
Was my first merge with git.  
All works fine (auto-merge) but for test/x3/container_support.cpp I had to resolve conflicts because we both changed the file.

---

## Comment 22

> Username: djowel  
> Created_at: 2016-03-30 22:30:11 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#issuecomment-203667753  

Here's the current state of Spirit: https://github.com/boostorg/spirit. Compare it with yours.

---

## Comment 23

> Username: octopus-prime  
> Created_at: 2016-03-30 22:32:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#issuecomment-203668275  

About the sync with boostorg/spirit...  
  
git remote add upstream https://github.com/boostorg/spirit.git  
git fetch upstream  
git pull upstream develop  
  
Than merge if conflicts. Than commit & push. Correct?!

---

## Comment 24

> Username: djowel  
> Created_at: 2016-03-30 22:35:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#issuecomment-203669232  

Honestly, command-line git makes me crazy ;-) There's a 1000 ways to shoot yourself. I use a GUI. You might want to ask someone else. I'm not (and never will be) a git expert.

---

## Comment 25

> Username: octopus-prime  
> Created_at: 2016-03-30 22:37:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#issuecomment-203669825  

LOL! Me too. I use eclipse all the time. But for sync with boostorg I had to use command line :-(  
  
However... I think this pull request is broken.  
  
What about closing this pull request without merging?  
I can make another pull request with same changes (using BOOST TTI).

---

## Comment 26

> Username: octopus-prime  
> Created_at: 2016-03-30 22:39:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#issuecomment-203670183  

Of course I have to do a new fork too, because my copy of spirit is broken too.

---

## Comment 27

> Username: djowel  
> Created_at: 2016-03-30 22:40:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#issuecomment-203670398  

Sounds good! Thanks!

---

## Comment 28

> Username: octopus-prime  
> Created_at: 2016-03-30 22:44:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/182#issuecomment-203671233  

I close this pull request.

---
