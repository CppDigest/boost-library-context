# #20 [X3 devel] facade API [Closed]

> Username: jamboree  
> Created at: 2014-05-01 04:21:14 UTC  
> Updated at: 2014-07-20 21:59:27 UTC  
> Closed at: 2014-05-02 13:17:25 UTC  
> Url: https://github.com/boostorg/spirit/pull/20  

### Take 2  
  
Only contains the basis for facade API.  
core/action is untouched.

---

## Comment 1

> Username: djowel  
> Created_at: 2014-05-01 21:56:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/20#issuecomment-41961623  

We've been discussing in the spirit-dev IRC. We we're hoping for a even finer grained PRs and with a single commit instead of various push/patches that's difficult to understand and review. Let's focus on utilities for now. Could yo please redo that in a single commit?

---

## Comment 2

> Username: K-ballo  
> Created_at: 2014-05-01 21:59:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/20#issuecomment-41961938  

The "single commit" piece does not refer to the number of commits in the pull request, but to the way changes are handled. In this request, commits `28764b3` and `362c452` should instead be a single commit so that they can be reviewed as a whole. Otherwise it's difficult to follow a single logical change split across interleaved commits.

---

## Comment 3

> Username: djowel  
> Created_at: 2014-05-01 22:04:33 UTC  
> Updated_at: 2014-05-01 22:05:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/20#issuecomment-41962335  

To be clear, I'd like to focus on only 'utilities' for now. Let's deal with 'caller', 'parser' and 'directive' later. I appreciate your excitement over this, but let's take it one step at a time. These are core infrastructure that need to be well thought and executed.

---
