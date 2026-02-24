# #126 add unit test and release notes about #119  [Closed]

> Username: pauldreik  
> Created at: 2018-10-06 18:54:36 UTC  
> Updated at: 2018-11-18 20:01:09 UTC  
> Closed at: 2018-11-17 19:46:56 UTC  
> Url: https://github.com/boostorg/serialization/pull/126  

I added a simplified version of the code by @sgiraudot in https://github.com/boostorg/serialization/issues/119#issue-363122998. The unit test triggers null pointer dereference in 1.65 but not latest develop as of now.  
Besides the unit test, I added information to the release notes as this is exactly the kind of information at least I expect to see there. I read the release notes to find out if it is relevant to upgrade to a new boost version. Avoiding undefined behaviour in my code is certainly a reason to upgrade!  
  
Edit: I realize I accidentally used nullptr which would break pre C++ 11 builds, is this a problem?

---

## Review 1 [Changes requested]

> Username: jeking3  
> Created_at: 2018-11-07 21:36:47 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/serialization/pull/126#pullrequestreview-172710725  

📁 test/test_member_pointer_issue119.cpp

```diff
  28 |+ struct Node
  29 |+ {
  30 |+   Node* ptr=nullptr;
```

> Username: jeking3  
> Created_at: 2018-11-07 21:36:43 UTC  
> Updated_at: 2018-11-07 21:36:47 UTC  
> Url: https://github.com/boostorg/serialization/pull/126#discussion_r231685735  

Change this so it works on C++03.


---

## Comment 2

> Username: robertramey  
> Created_at: 2018-11-17 19:46:56 UTC  
> Url: https://github.com/boostorg/serialization/pull/126#issuecomment-439642748  

Since this issue was resolved over a year ago, I'm not going to add in this tests.  I see you're a first time contributor.  My GitHub foo is not enough to know whether this is first time for this project or first time contributor to GitHub.  But no matter.  I appreciate your efforts and don't want to discourage further contributions in the future.  You've done a nice job here.

---

## Comment 3

> Username: pauldreik  
> Created_at: 2018-11-18 20:01:09 UTC  
> Url: https://github.com/boostorg/serialization/pull/126#issuecomment-439721121  

Ok, about the unit test, but what about the additions to the release notes? That is the exact type of information I am looking for, when considering boost upgrades. I thinks it is more important,  
the more capable the compilers get taking advantage of undefined behaviour. As stated in #119, it actually happens to real people, it's not hypothetical. Please reconsider incorporating this into the release notes.  
Thanks,  
Paul

---
