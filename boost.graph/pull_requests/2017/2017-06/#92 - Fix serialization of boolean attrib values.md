# #92 Fix serialization of boolean attrib values [Open]

> Username: hhussienn  
> Created at: 2017-06-08 03:30:01 UTC  
> Updated at: 2018-11-06 22:58:26 UTC  
> Url: https://github.com/boostorg/graph/pull/92  

Fix serialization of boolean attribute values from '1' or '0' to 'true' or 'false' per this ticket: https://svn.boost.org/trac/boost/ticket/11092

---

## Comment 1

> Username: anadon  
> Created_at: 2018-08-31 17:55:25 UTC  
> Url: https://github.com/boostorg/graph/pull/92#issuecomment-417743176  

I'm helping out with the PR backlog. Looks like you have a code change, no test, and no new examples will be needed.  Is this needed, or more semantics?  I'm wary to have things change unless there is a need.  That isn't to say that this is bad, but I need to be careful with this kind of PR.  This is to let you know and help me prioritize PR's.

---

## Comment 2

> Username: anadon  
> Created_at: 2018-10-15 19:59:38 UTC  
> Url: https://github.com/boostorg/graph/pull/92#issuecomment-429992184  

@kinghussien Can you rebase your PR on devel?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2018-10-16 17:28:02 UTC  
> Url: https://github.com/boostorg/graph/pull/92#issuecomment-430325488  

I'm going to close and re-open to trigger a CI build.  
  
Question - while this looks sensible (and reasonably innocuous to me) - do we have a way to test this?  Based on a quick look, I couldn't see anything in the GraphML spec that requires boolean attributes to be either boolalpha or numeric.  I would hope that most software would support both.... anyone have any experience of this?

---

## Comment 4

> Username: anadon  
> Created_at: 2018-11-01 14:06:20 UTC  
> Url: https://github.com/boostorg/graph/pull/92#issuecomment-435050990  

@jzmaddock gcc6 is timing out a lot.  Should it be disabled?  I may fork this, rebase on devel, and make a PR in order to close this out.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2018-11-01 18:23:50 UTC  
> Url: https://github.com/boostorg/graph/pull/92#issuecomment-435137697  

I've pushed a fix for the timeout to develop.  
  
Any thoughts on my comments above?

---

## Comment 6

> Username: anadon  
> Created_at: 2018-11-01 19:34:10 UTC  
> Url: https://github.com/boostorg/graph/pull/92#issuecomment-435159411  

I don't have experience.  Might the graphviz people have any experience?

---

## Comment 7

> Username: anadon  
> Created_at: 2018-11-05 21:08:16 UTC  
> Url: https://github.com/boostorg/graph/pull/92#issuecomment-436035373  

@jzmaddock Looking into this further, I think NetworkX not working is significant enough to pull in the change.  If this does break code, it will be very easy to fix, but surely adds further integration and support with tools in the area.  I'll vote to merge it in.

---

## Comment 8

> Username: anadon  
> Created_at: 2018-11-06 22:58:25 UTC  
> Url: https://github.com/boostorg/graph/pull/92#issuecomment-436440263  

@jzmaddock Could you trigger the failed test?  It was a time out.

---
