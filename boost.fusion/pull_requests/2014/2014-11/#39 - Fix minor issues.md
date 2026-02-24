# #39 Fix minor issues [Closed]

> Username: Flast  
> Created at: 2014-11-17 17:19:14 UTC  
> Updated at: 2014-11-19 02:07:12 UTC  
> Closed at: 2014-11-18 17:00:48 UTC  
> Url: https://github.com/boostorg/fusion/pull/39  

- cc9b043 **_result_of::size::value and result_of::size::type::value should be same type, rels to: [#7304](https://svn.boost.org/trac/boost/ticket/7304)._**  
  - Fix metafunction type mismatch, which is described in the ticket. However, still stay on `wontfix` status.  
- 6881be8 **_result_of::at<Seq, N>::type is't defined when sizeof of sequence is less than N, close [#6507](https://svn.boost.org/trac/boost/ticket/6507), [#7651](https://svn.boost.org/trac/boost/ticket/7651)_**  
  - Fix Out-of-Bounds sequence acessing via `at` and `at_c`.  
  - `result_of::at_c<sequence<int, char>, 2>::type` (and counterpart of `at_c`) doesn't occur hard error (static assert), but doesn't compile (SFINAE-friendly).  
- 6e795e6 **_Mitigate too noisy name-hiding warnings (C4453) on VS2015 Preview._**  
  - Rename all preprocessed parameter name `_n` to `_arg_n`; reported by @K-ballo .  
- Fix regression with `fusion::pair` and move.  
  - 8bcfa8c reverts 63045e8, and 812c027 partially reverts f3fa7a1  
  - This regression introduced recent `fusion::pair`'s change.  
  - facf65b  **_Avoid to elect templated move-ctor as a copy-ctor._**  
    - And, fix unexpected ctor election correctly.

---

## Comment 1

> Username: djowel  
> Created_at: 2014-11-18 01:06:34 UTC  
> Url: https://github.com/boostorg/fusion/pull/39#issuecomment-63406271  

OK, _arg_N is arguably easier to read and understandable than _0. Is there any other reason apart from readability that you are chaining this? I'd say it's an unnecessary change, but I can probably accept the change if you remove the underscores. I.e. arg0, arg1, etc. Same for _, just use arg.

---

## Comment 2

> Username: djowel  
> Created_at: 2014-11-18 01:07:54 UTC  
> Url: https://github.com/boostorg/fusion/pull/39#issuecomment-63406375  

Also, I can't review all the changes. I'd appreciate it if you can break this PR into smaller chunks.

---

## Comment 3

> Username: Flast  
> Created_at: 2014-11-18 02:33:50 UTC  
> Url: https://github.com/boostorg/fusion/pull/39#issuecomment-63413542  

> I can probably accept the change if you remove the underscores. I.e. arg0, arg1, etc. Same for _, just use arg.  
  
I afraid to `arg0, arg1, ...` conflict again (Boost.Phienix's argument expression).  
![C4453 testing](http://gyazo.flast.jp/d126052c7af931f9a40cdcfed1834d1c.png)  
  
I believe redundant underscore-ed symbols are not defined anywhere.  
  
> Also, I can't review all the changes. I'd appreciate it if you can break this PR into smaller chunks.  
  
OK, I will break that commit.

---

## Comment 4

> Username: djowel  
> Created_at: 2014-11-18 05:11:07 UTC  
> Url: https://github.com/boostorg/fusion/pull/39#issuecomment-63423651  

Phoenix argN is in its own namespace, not the global. There should be no conflict there. Even _N should not be a problem other than the warning which can and should be suppressed.

---

## Comment 5

> Username: Flast  
> Created_at: 2014-11-18 06:21:09 UTC  
> Url: https://github.com/boostorg/fusion/pull/39#issuecomment-63427748  

> Phoenix argN is in its own namespace, not the global.  
  
Yes. However, when user use 'using namespace boost:: phoenix;', the warnings are appeared again. See screenshot of prev my comment.

---

## Comment 6

> Username: djowel  
> Created_at: 2014-11-18 07:09:35 UTC  
> Url: https://github.com/boostorg/fusion/pull/39#issuecomment-63430774  

To be accurate, phoenix placeholders are in namespace boost::phoenix::placeholders. In any case, if the user starts to use a using namespace, then it is his responsibility and he should know the consequences. It is not the library's responsibility to avoid any and all name clashes as long as they are not in the global namespace. What boost bind did is wrong to put the placeholders in global space, but Phoenix nor Fusion never committed that mistake.

---

## Comment 7

> Username: Flast  
> Created_at: 2014-11-18 09:38:43 UTC  
> Url: https://github.com/boostorg/fusion/pull/39#issuecomment-63444168  

> In any case, if the user starts to use a using namespace, then it is his responsibility and he should know the consequences.  
  
Indeed. OK, I use argN instead _arg_N.

---

## Comment 8

> Username: Flast  
> Created_at: 2014-11-18 16:04:42 UTC  
> Url: https://github.com/boostorg/fusion/pull/39#issuecomment-63494354  

OK, now I update the commit.  
  
Changes from original PR  
- Use `argN` instead `_arg_N`.  
- Don't preprocess changed files to review easier.  
  - Please run preprocess after approved.

---

## Comment 9

> Username: djowel  
> Created_at: 2014-11-18 16:27:12 UTC  
> Updated_at: 2014-11-18 16:30:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/39#discussion_r20517331  

Shouldn't this be arg for consistency (instead of _) ?

---

## Comment 10

> Username: djowel  
> Created_at: 2014-11-18 16:27:43 UTC  
> Updated_at: 2014-11-18 16:30:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/39#discussion_r20517367  

Change _ to arg for consistency?

---

## Comment 11

> Username: djowel  
> Created_at: 2014-11-18 16:28:39 UTC  
> Updated_at: 2014-11-18 16:30:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/39#discussion_r20517429  

_arg_ ?? or arg ?

---

## Comment 12

> Username: djowel  
> Created_at: 2014-11-18 16:28:51 UTC  
> Updated_at: 2014-11-18 16:30:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/39#discussion_r20517446  

arg ?? or arg ?

---

## Comment 13

> Username: djowel  
> Created_at: 2014-11-18 16:29:25 UTC  
> Updated_at: 2014-11-18 16:30:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/39#discussion_r20517477  

_arg_ ??

---

## Comment 14

> Username: djowel  
> Created_at: 2014-11-18 16:30:08 UTC  
> Updated_at: 2014-11-18 16:30:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/39#discussion_r20517513  

_arg_?

---

## Comment 15

> Username: djowel  
> Created_at: 2014-11-18 16:33:13 UTC  
> Url: https://github.com/boostorg/fusion/pull/39#discussion_r20517722  

I don't think this is necessary. A static assert is better. Please move this to a new PR for discussion. This will affect compile time.

---

## Comment 16

> Username: djowel  
> Created_at: 2014-11-18 16:36:33 UTC  
> Url: https://github.com/boostorg/fusion/pull/39#discussion_r20517973  

I removed this ctor. This is no longer necessary because the ctor above it is simpler and sufficient. A better implementation would be a && version of the ctor above this.

---

## Comment 17

> Username: Flast  
> Created_at: 2014-11-18 16:44:35 UTC  
> Url: https://github.com/boostorg/fusion/pull/39#discussion_r20518559  

No, original source doesn't use the parameter `data` (see line 77). Thus original caller's (i.e. line 127) argument doesn't make sense (It worked fine fortunately, so far!).  
  
If you want to rename that parameter name to `arg`, we should rename all of other macro's parameter name for consistency.

---

## Comment 18

> Username: djowel  
> Created_at: 2014-11-18 16:45:35 UTC  
> Url: https://github.com/boostorg/fusion/pull/39#issuecomment-63501921  

There's still too much here. Can we please break this down into small PRs for separate concerns? That way, we can discuss better.

---

## Comment 19

> Username: Flast  
> Created_at: 2014-11-18 17:00:48 UTC  
> Url: https://github.com/boostorg/fusion/pull/39#issuecomment-63504599  

OK, I will break this PR into four.

---

## Comment 20

> Username: djowel  
> Created_at: 2014-11-19 00:29:31 UTC  
> Url: https://github.com/boostorg/fusion/pull/39#discussion_r20550542  

No, you are right! This is a better implementation! I applied this bit to develop branch.

---

## Comment 21

> Username: Flast  
> Created_at: 2014-11-19 02:07:12 UTC  
> Url: https://github.com/boostorg/fusion/pull/39#discussion_r20554004  

Thank you! and sorry for my not good explaining.

---
