# #111 Null pointer when reindexing edge list [Closed]

> Username: raahlb  
> Created at: 2018-07-30 10:18:13 UTC  
> Updated at: 2018-10-12 18:16:28 UTC  
> Closed at: 2018-10-12 18:16:28 UTC  
> Url: https://github.com/boostorg/graph/pull/111  

Don't copy value_type if not needed, as source's m_property will be set to null when copied.  
  
See https://svn.boost.org/trac10/ticket/13544.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2018-08-19 02:28:53 UTC  
> Url: https://github.com/boostorg/graph/pull/111#issuecomment-414098615  

Would it be relatively easy to add a test-case that fails without this fix?

---

## Comment 2

> Username: raahlb  
> Created_at: 2018-08-23 14:28:46 UTC  
> Url: https://github.com/boostorg/graph/pull/111#issuecomment-415437101  

I added a test case. Also added it to the Jamfile.v2. Not used to this test system, so please verify I did it in the right way.

---

## Comment 3

> Username: anadon  
> Created_at: 2018-08-31 17:31:24 UTC  
> Url: https://github.com/boostorg/graph/pull/111#issuecomment-417736711  

I'm helping out with the PR backlog.  Looks like you have a bugfix, test, and no new examples will be needed.  This will be in the first batch of merged PR's.  This is to let you know and help me prioritize PR's.

---

## Comment 4

> Username: anadon  
> Created_at: 2018-09-01 03:39:01 UTC  
> Updated_at: 2018-09-01 03:39:12 UTC  
> Url: https://github.com/boostorg/graph/pull/111#issuecomment-417829791  

Your test file was not included in the test directory.  Make sure you added it to git.

---

## Comment 5

> Username: anadon  
> Created_at: 2018-09-01 03:41:40 UTC  
> Updated_at: 2018-09-01 03:41:53 UTC  
> Url: https://github.com/boostorg/graph/pull/111#issuecomment-417829899  

I see what happened.  The test you added has a different file name than the test file you added.  I'll just fix that up.

---

## Comment 6

> Username: raahlb  
> Created_at: 2018-09-02 10:35:11 UTC  
> Updated_at: 2018-09-02 10:42:14 UTC  
> Url: https://github.com/boostorg/graph/pull/111#issuecomment-417920227  

Missed that you wrote that you had fixed it yourself. Not super important, but the name "delete edge" is a bit misleading, it's actually a vertex that is being deleted in the test. Sorry about that.  
  
Thanks!

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2018-10-12 17:39:36 UTC  
> Url: https://github.com/boostorg/graph/pull/111#issuecomment-429403937  

Are these changes in https://github.com/boostorg/graph/pull/114?  If so this can be closed now.

---

## Comment 8

> Username: anadon  
> Created_at: 2018-10-12 18:13:25 UTC  
> Url: https://github.com/boostorg/graph/pull/111#issuecomment-429413394  

These changes are in #114 and so this should be closed.

---
