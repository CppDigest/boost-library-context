# #95 Improve documentation [Closed]

> Username: alandefreitas  
> Created at: 2021-12-30 01:12:58 UTC  
> Updated at: 2022-01-09 17:17:22 UTC  
> Closed at: 2022-01-08 22:37:46 UTC  
> Url: https://github.com/boostorg/url/pull/95  

This is really a draft. Locally, I still have some notes with questions to continue with this documentation. I didn't commit these but we should probably discuss them before moving on.  
  
Some other commits in this PR fix small quickbook and doxygen problems in passing. We could cherry-pick some of these but there's nothing critical in there.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2021-12-30 01:19:27 UTC  
> Url: https://github.com/boostorg/url/pull/95#issuecomment-1002833628  

An automated preview of the documentation is available at [https://95.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://95.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-01-05 01:14:30 UTC  
> Url: https://github.com/boostorg/url/pull/95#issuecomment-1005298841  

An automated preview of the documentation is available at [https://95.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://95.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-01-05 17:25:59 UTC  
> Url: https://github.com/boostorg/url/pull/95#issuecomment-1005925576  

Can we please rebase and squash this down?

---

## Comment 4

> Username: alandefreitas  
> Created_at: 2022-01-05 18:25:14 UTC  
> Updated_at: 2022-01-05 18:31:28 UTC  
> Url: https://github.com/boostorg/url/pull/95#issuecomment-1005972199  

> Can we please rebase and squash this down?  
  
I'm rebasing it but I wasn't planning to squash it down while this is still a draft. The reason is this PR includes some unrelated stuff and it was my understanding that you'd rather have that go into smaller PRs.

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-01-05 18:39:27 UTC  
> Url: https://github.com/boostorg/url/pull/95#issuecomment-1005982471  

An automated preview of the documentation is available at [https://95.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://95.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2022-01-05 23:29:35 UTC  
> Url: https://github.com/boostorg/url/pull/95#issuecomment-1006158573  

An automated preview of the documentation is available at [https://95.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://95.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-01-06 00:09:27 UTC  
> Url: https://github.com/boostorg/url/pull/95#issuecomment-1006176998  

An automated preview of the documentation is available at [https://95.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://95.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 8

> Username: alandefreitas  
> Created_at: 2022-01-06 00:12:59 UTC  
> Url: https://github.com/boostorg/url/pull/95#issuecomment-1006178685  

So the PR has been rebased. The commits were squashed into fewer commits containing each of the unrelated changes in this PR:  
  
- Include the C++11 requirement in the CMake target  
- Fix lots of small quickbook and doxygen warnings  
- Updated guidelines in javadoc.hpp  
- Javadoc for `string_value`, `params_encoded`, and `params` (The javadoc is better than before but there are still some guidelines from javadoc.hpp missing)  
  
If any of these changes are considered useful while this PR is still a draft, I can cherry-pick them into another branch and open another PR. If everything is considered useful, I can squash them into a single commit.  
  
(This PR contains all these unrelated changes mostly because I wanted to take advantage of the common documentation preview while this is still a draft.)

---

## Comment 9

> Username: codecov[bot]  
> Created_at: 2022-01-06 02:18:03 UTC  
> Updated_at: 2022-01-07 22:47:34 UTC  
> Url: https://github.com/boostorg/url/pull/95#issuecomment-1006230585  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/95?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#95](https://codecov.io/gh/CPPAlliance/url/pull/95?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (3aae915) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/b63bba8dfd5099cf2feef75361886c32e66866d1?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (b63bba8) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head 3aae915 differs from pull request most recent head b46d9b4. Consider uploading reports for the commit b46d9b4 to get more accurate results  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/95/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/95?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #95   +/-   ##  
========================================  
  Coverage    96.59%   96.59%             
========================================  
  Files           84       84             
  Lines         5319     5319             
========================================  
  Hits          5138     5138             
  Misses         181      181             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/95?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/authority\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/95/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvYXV0aG9yaXR5X3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/params.hpp](https://codecov.io/gh/CPPAlliance/url/pull/95/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXMuaHBw) | `99.37% <ø> (ø)` | |  
| [include/boost/url/impl/params\_encoded.hpp](https://codecov.io/gh/CPPAlliance/url/pull/95/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfZW5jb2RlZC5ocHA=) | `99.35% <ø> (ø)` | |  
| [include/boost/url/impl/string.hpp](https://codecov.io/gh/CPPAlliance/url/pull/95/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9zdHJpbmcuaHBw) | `94.31% <ø> (ø)` | |  
| [include/boost/url/ipv6\_address.hpp](https://codecov.io/gh/CPPAlliance/url/pull/95/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaXB2Nl9hZGRyZXNzLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/params\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/95/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW1zX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/string.hpp](https://codecov.io/gh/CPPAlliance/url/pull/95/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvc3RyaW5nLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url.hpp](https://codecov.io/gh/CPPAlliance/url/pull/95/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsLmhwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/95?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/95?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [b63bba8...b46d9b4](https://codecov.io/gh/CPPAlliance/url/pull/95?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-07 19:12:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/95#pullrequestreview-846913686  

📁 CMakeLists.txt

```diff
  48 | function(boost_url_setup_properties target)
  49 |     target_compile_features(${target} PUBLIC cxx_constexpr)
  50 |+     target_compile_features(${target} PUBLIC cxx_std_11)
```

> Username: vinniefalco  
> Created_at: 2022-01-07 19:12:52 UTC  
> Updated_at: 2022-01-07 19:12:53 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780472626  

Why are you fiddling with the CML?

> Username: alandefreitas  
> Created_at: 2022-01-07 22:21:22 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780561278  

The problem is because this is wrong or because it's unrelated to the PR?

> Username: vinniefalco  
> Created_at: 2022-01-07 22:24:09 UTC  
> Updated_at: 2022-01-07 22:24:10 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780562368  

I have no idea, I am not a cmake expert, so I don't know what impact this has. And the other libraries don't have this line. So why don't they? The people who worked on the CMakeLists.txt decided not to include it. Why? Maybe it is not available in older cmakes. Maybe it causes a problem with the Boost superproject. Maybe they just didn't know it exists. In any event, this change is unrelated to improving documentation and it needs to be in its own pull request, reviewed by people who know Cmake better than I do (and who know the impact on the Boost superproject).

> Username: alandefreitas  
> Created_at: 2022-01-07 22:25:03 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780562741  

Assuming it's because it's unrelated to the PR, this is because this draft was moved from the fork because the PR in the fork didn't get the preview. The intention of the draft was to share the state of the documentation and so you could have a look at what I have locally to give me instructions. If anything is useful, I would just cherry-pick it into another PR. In any case, this doesn't seem to be working. I'll just close this PR then.

> Username: alandefreitas  
> Created_at: 2022-01-07 22:52:28 UTC  
> Updated_at: 2022-01-07 22:52:29 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780572372  

> this change is unrelated to improving documentation   
  
Since (i) this is a sufficient condition to remove the `cxx_std_11` requirement from this target and (ii) I'm closing this PR anyway, I won't argue in favor of it.

> Username: vinniefalco  
> Created_at: 2022-01-09 17:11:50 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780808055  

the c++11 change should be opened as its own separate pull request


---

## Comment 11

> Username: vinniefalco  
> Created_at: 2022-01-07 19:13:56 UTC  
> Updated_at: 2022-01-07 19:14:07 UTC  
> Url: https://github.com/boostorg/url/pull/95#issuecomment-1007668252  

Changes to CMakeLists.txt should be in a separate pull request, in a single commit. And to be honest I am not feeling them especially all the comments.

---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-07 19:14:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/95#pullrequestreview-846914822  

📁 doc/javadoc.hpp

```diff
  70 |+         @ref {refid},
  71 |+         @ref {refid}.
  72 |+ */
```

> Username: vinniefalco  
> Created_at: 2022-01-07 19:14:23 UTC  
> Updated_at: 2022-01-07 19:14:25 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780473382  

missing linefeed


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-07 19:14:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/95#pullrequestreview-846914984  

📁 doc/qbk/1.1.quicklook.qbk

```diff
  15 | Say you have the following URL that you want to parse:
  16 |+ 
  17 |+ [teletype]
```

> Username: vinniefalco  
> Created_at: 2022-01-07 19:14:36 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780473484  

why `[teletype]`? This should not be needed

> Username: alandefreitas  
> Created_at: 2022-01-07 22:27:38 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780563632  

Without teletype, quickbook assumes this is c++. This generates a warning and highlights the syntax incorrectly.

> Username: vinniefalco  
> Created_at: 2022-01-07 22:29:36 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780564344  

Ok so it is just for the non-c++ blocks. makes sense

> Username: alandefreitas  
> Created_at: 2022-01-07 23:02:36 UTC  
> Updated_at: 2022-01-07 23:02:37 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780576007  

Yes. This command tells quickbook the next block is plain text.

> Username: alandefreitas  
> Created_at: 2022-01-07 23:03:22 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780576209  

Quickbook chose the worst keyword possible for that here, but I have no power over that.

> Username: vinniefalco  
> Created_at: 2022-01-09 17:12:20 UTC  
> Updated_at: 2022-01-09 17:12:21 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780808108  

we need this change


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-07 19:15:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/95#pullrequestreview-846915733  

📁 include/boost/url/authority_view.hpp

```diff
1226 |     std::ostream& os,
1227 |-     authority_view const& a);
1227 |+     authority_view const& u);
```

> Username: vinniefalco  
> Created_at: 2022-01-07 19:15:36 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780474017  

What's the point of renaming this? I was going to use `a` as the canonical variable name for authority views.

> Username: alandefreitas  
> Created_at: 2022-01-07 22:29:16 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780564241  

The var name wasn't matching the documentation. This generates a doxygen warning. So I'll change the documentation instead.


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-07 19:16:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/95#pullrequestreview-846916031  

📁 include/boost/url/impl/params.hpp

```diff
 288 | //------------------------------------------------
 289 | 
 290 |+ inline
```

> Username: vinniefalco  
> Created_at: 2022-01-07 19:16:00 UTC  
> Updated_at: 2022-01-07 19:16:01 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780474227  

these were already marked inline in the first declaration, or else how do you think that this would link?

> Username: alandefreitas  
> Created_at: 2022-01-07 22:31:54 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780565209  

I moved the keywords to the implementation. This was generating a warning in static analysis: https://isocpp.org/wiki/faq/inline-functions#where-to-put-inline-keyword

> Username: vinniefalco  
> Created_at: 2022-01-07 22:34:09 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780566031  

hmm......... where is this warning? what does boost.json do?

> Username: alandefreitas  
> Created_at: 2022-01-07 22:56:50 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780574066  

> hmm......... where is this warning?   
  
It's not a warning in our tooling. I don't even think we have common static analysis in our tooling. This is local. The tool points me to the isocpp guidelines in https://isocpp.org/wiki/faq/inline-functions#where-to-put-inline-keyword  
  
The guideline seems reasonable unless there's another motivation I don't know about. But I can undo that if you want, of course. I'll just disable it locally so it stops annoying me.

> Username: vinniefalco  
> Created_at: 2022-01-07 23:15:09 UTC  
> Updated_at: 2022-01-07 23:15:10 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780579907  

if we change it, we have to change it not only globally in url but also http_proto, json, and beast

> Username: alandefreitas  
> Created_at: 2022-01-07 23:21:48 UTC  
> Updated_at: 2022-01-07 23:21:49 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780581807  

> if we change it, we have to change it not only globally in url but also http_proto, json, and beast  
  
Well... that's not up to me then. I'll undo it and disable the warning locally.

> Username: vinniefalco  
> Created_at: 2022-01-08 06:06:58 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780637123  

Lets open an issue for it, so we can track it

> Username: alandefreitas  
> Created_at: 2022-01-08 22:35:27 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780713591  

It's already undone, but you can let me know if I should redo it at any time.   
  
My understanding is the isocpp guideline is correct and the reason Boost.URL does it seems to be because it's replicating a pattern from ASIO that doesn't apply here. To the best of my knowledge, Boost.URL doesn't have a macro intended to enable and disable these specifiers depending on how the ipp files are going to be used.  
  
In any case, I don't think the issue is useful at this point because you have already stated you don't consider this to be an issue in this lib.


---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-07 19:16:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/95#pullrequestreview-846916365  

📁 include/boost/url/params_view.hpp

```diff
  44 |     class iterator;
  45 | 
  46 |+     /** The value_type of query parameters
```

> Username: vinniefalco  
> Created_at: 2022-01-07 19:16:27 UTC  
> Updated_at: 2022-01-07 19:16:28 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780474483  

If we are going to accept this brief then it should say "The value type of query parameters"


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-07 19:17:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/95#pullrequestreview-846917339  

📁 include/boost/url/string.hpp

```diff
  28 |+ 
  29 |+     They are reference counted to
  30 |+     makes copies more efficient.
```

> Username: vinniefalco  
> Created_at: 2022-01-07 19:17:41 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780475156  

saying that copies are "more efficient" is worse than saying "cheap." More efficient could mean that copies are 10 times faster. Which is definitely more efficient, but is not "cheap" (consider copying a 10 megabyte string). "cheap" has a specific meaning in C++. For example, copying an iterator is supposed to be "cheap." Copying a string view is "cheap". etc.

> Username: alandefreitas  
> Created_at: 2022-01-07 22:34:08 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780566029  

So should I say "cheap" or "constant time"?

> Username: vinniefalco  
> Created_at: 2022-01-07 23:23:27 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780582319  

I like "cheap" because "constant time" sounds more precise, and I don't want to paint myself into a corner making complexity claims. Sometimes I do make complexity claims (for example in JSON) and in that case "constant time" is appropriate, but note that when you say "constant time" you are baking that into the public interface. If the algorithm changes to be no longer constant time, then that is technically breaking programs that depend on the complexity guarantee.  
  
In short, "cheap" gives us some needed wiggle room and communicates the idea sufficiently, without locking us into making a guarantee.

> Username: alandefreitas  
> Created_at: 2022-01-07 23:37:36 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780586349  

> "cheap" has a specific meaning in C++  
  
> I like "cheap" because "constant time" sounds more precise  
  
What's the specific meaning of "cheap" in C++ then? Less than O(log n)?


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-07 19:18:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/95#pullrequestreview-846917675  

📁 include/boost/url/string.hpp

```diff
  59 |+ 
  60 |+      */
  61 |     struct base;
```

> Username: vinniefalco  
> Created_at: 2022-01-07 19:18:10 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780475379  

Did you document a private nested type?

> Username: alandefreitas  
> Created_at: 2022-01-07 22:38:46 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780567758  

I usually document everything, even the private stuff. It works like a well-written comment, with a few advantages 1) that it works in IDE for developers with quick documentation plugins, 3) it works for people and developers navigating the source code, 3) it's already documented if we decide to make it public, 4) it's already documented if we decide to extract any private members in doxygen.   
  
I can make it a comment or remove it if you want. No passion here.

> Username: vinniefalco  
> Created_at: 2022-01-07 22:44:45 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780569798  

We should never document private stuff, because the return on the investment is vanishingly small. First of all maintainers have a much better grasp of the library and its implementation than users. Second, users get zero value from documentation on private members. However, we should still use comments to explain non-obvious things in the implementation (inside function definitions). Almost always these comments should state "why" something is being done. Here's an example, we skip incrementing `it`:  
https://github.com/vinniefalco/http_proto/blob/f2c3a5d9bfb49060af467b65843e125e6db958e2/include/boost/http_proto/rfc/impl/field_rule.ipp#L152

> Username: alandefreitas  
> Created_at: 2022-01-07 23:00:59 UTC  
> Updated_at: 2022-01-07 23:01:00 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780575474  

> We should never document private stuff, because the return on the investment is vanishingly small  
  
I wouldn't document everything that is private. I agree with the argument for not writing it. In this case, and only in this case, the marginal cost is zero because it was already done. So the return on investment in `NaN`.  
  
> However, we should still use comments to explain non-obvious things in the implementation (inside function definitions). Almost always these comments should state "why" something is being done.  
  
I can't disagree with that.

> Username: vinniefalco  
> Created_at: 2022-01-09 17:15:35 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780808484  

keep the file


---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-07 19:32:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/95#pullrequestreview-846928839  

📁 include/boost/url/string.hpp

```diff
 123 |+         This function decrements the string_value reference counter and
 124 |+         destroys the underlying string if no other instance of @ref string_value
 125 |+         refers to the same underlying string.
```

> Username: vinniefalco  
> Created_at: 2022-01-07 19:32:23 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780482731  

Upon destruction, ownership of the shared string is released. If there are no more owners, the resources for the string are released.

> Username: vinniefalco  
> Created_at: 2022-01-07 19:32:44 UTC  
> Updated_at: 2022-01-07 19:32:45 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780482895  

We usually never talk about reference counting, but instead discuss "shared ownership"


---

## Review 20 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-07 19:33:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/95#pullrequestreview-846929322  

📁 include/boost/url/string.hpp

```diff
  21 | namespace urls {
  22 | 
  23 |- /** A read-only, reference counted string
```

> Username: vinniefalco  
> Created_at: 2022-01-07 19:33:02 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780483062  

This should say "A const string with shared ownership"


---

## Review 21 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-07 22:24:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/95#pullrequestreview-847041993  

📁 doc/javadoc.hpp

```diff
  12 |     {description}
  13 |+     <!-- Assume the user knows C++ -->
  14 |+     <!-- Assume the user read the whole exposition -->
```

> Username: vinniefalco  
> Created_at: 2022-01-07 22:24:37 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780562553  

read or "will read"

> Username: alandefreitas  
> Created_at: 2022-01-07 22:44:00 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780569552  

I meant "read" in the past tense. Or a user who "has read" the whole exposition.   
  
I inferred this rule in the last PR, as you told me to assume in the reference API, the user has read the whole exposition.

> Username: vinniefalco  
> Created_at: 2022-01-07 23:18:51 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780581016  

well that's why I say "has read" _or_ "will read"

> Username: alandefreitas  
> Created_at: 2022-01-07 23:23:20 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780582283  

Exactly. I've changed it to "has read". The present tense would be "reads" here, so it still wouldn't be ambiguous, but "has read" is better.


---

## Comment 22

> Username: cppalliance-bot  
> Created_at: 2022-01-07 22:54:27 UTC  
> Url: https://github.com/boostorg/url/pull/95#issuecomment-1007802321  

An automated preview of the documentation is available at [https://95.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://95.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 23

> Username: vinniefalco  
> Created_at: 2022-01-07 23:19:25 UTC  
> Url: https://github.com/boostorg/url/pull/95#issuecomment-1007814182  

Lets not put backticks in the commit message briefs because it makes them longer and less uniform

---

## Comment 24

> Username: alandefreitas  
> Created_at: 2022-01-07 23:25:05 UTC  
> Url: https://github.com/boostorg/url/pull/95#issuecomment-1007816805  

> Lets not put backticks in the commit message briefs because it makes them longer and less uniform  
  
Done.

---

## Comment 25

> Username: cppalliance-bot  
> Created_at: 2022-01-07 23:29:33 UTC  
> Url: https://github.com/boostorg/url/pull/95#issuecomment-1007818572  

An automated preview of the documentation is available at [https://95.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://95.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 26

> Username: cppalliance-bot  
> Created_at: 2022-01-07 23:44:30 UTC  
> Url: https://github.com/boostorg/url/pull/95#issuecomment-1007823209  

An automated preview of the documentation is available at [https://95.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://95.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 27

> Username: alandefreitas  
> Created_at: 2022-01-08 22:37:42 UTC  
> Url: https://github.com/boostorg/url/pull/95#issuecomment-1008167375  

I'm closing this since the draft preview is not very useful at the moment and everything else is either useless or wrong.

---

## Review 28 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-09 17:12:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/95#pullrequestreview-847283294  

📁 doc/qbk/2.8.authority_view.qbk

```diff
  27 | following authority string is valid for `parse_authority`:
  28 |+ 
  29 |+ [teletype]
```

> Username: vinniefalco  
> Created_at: 2022-01-09 17:12:31 UTC  
> Updated_at: 2022-01-09 17:12:32 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780808126  

need


---

## Review 29 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-09 17:12:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/95#pullrequestreview-847283315  

📁 include/boost/url/authority_view.hpp

```diff
1220 | 
1221 |-     @param u The URL to write
1221 |+     @param a The URL to write
```

> Username: vinniefalco  
> Created_at: 2022-01-09 17:12:41 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780808155  

need


---

## Review 30 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-09 17:12:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/95#pullrequestreview-847283330  

📁 include/boost/url/ipv6_address.hpp

```diff
 349 | 
 350 |-     @param addrr The address to write.
 350 |+     @param addr The address to write.
```

> Username: vinniefalco  
> Created_at: 2022-01-09 17:12:45 UTC  
> Updated_at: 2022-01-09 17:12:46 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780808165  

need


---

## Review 31 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-09 17:14:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/95#pullrequestreview-847283555  

📁 include/boost/url/params.hpp

```diff
 167 |+     /** <!-- @name Members -->
 168 |+         <!-- @{ -->
 169 |+      */
```

> Username: vinniefalco  
> Created_at: 2022-01-09 17:14:21 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780808343  

this grouping stuff doesn't work. please add this file back to the new PR


---

## Review 32 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-09 17:14:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/95#pullrequestreview-847283590  

📁 include/boost/url/params_encoded.hpp

```diff
  27 | #endif
  28 | 
  29 |+ /** A random-access handle to percent-encoded query parameters in a url.
```

> Username: vinniefalco  
> Created_at: 2022-01-09 17:14:39 UTC  
> Updated_at: 2022-01-09 17:14:40 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780808368  

Add this file back to the new PR please


---

## Review 33 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-09 17:15:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/95#pullrequestreview-847283652  

📁 include/boost/url/params_view.hpp

```diff
  53 |+ 
  54 |+         A special boolean member `has_value` is used to differentiate
  55 |+         empty strings from non-existent strings.
```

> Username: vinniefalco  
> Created_at: 2022-01-09 17:15:09 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780808415  

Add this back


---

## Review 34 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-09 17:15:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/95#pullrequestreview-847283675  

📁 include/boost/url/string.hpp

```diff
  32 |+     which provides compatibility with strings
  33 |+     in terms of comparisons and conversions.
  34 |+ 
```

> Username: vinniefalco  
> Created_at: 2022-01-09 17:15:22 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780808443  

keep this change


---

## Review 35 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-09 17:15:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/95#pullrequestreview-847283685  

📁 include/boost/url/string.hpp

```diff
  43 |     of the object are valid and remain
  41 |-     valid for the lifetime of the oriignal
  44 |+     valid for the lifetime of the original
```

> Username: vinniefalco  
> Created_at: 2022-01-09 17:15:28 UTC  
> Updated_at: 2022-01-09 17:15:29 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780808450  

keep this


---

## Review 36 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-09 17:16:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/95#pullrequestreview-847283760  

📁 include/boost/url/string.hpp

```diff
 132 |+         no string / nullptr.
 133 |+ 
 134 |+         @note As an optimization, the reference counter is `nullptr`.
```

> Username: vinniefalco  
> Created_at: 2022-01-09 17:16:13 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780808592  

"@note As an optimization, the reference counter is `nullptr`." could be a comment in the implementation but it doesn't go in a javadoc


---

## Review 37 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-09 17:16:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/95#pullrequestreview-847283812  

📁 include/boost/url/string.hpp

```diff
 146 |+         The buffer is destroyed after the last string_value points to it.
 147 |+ 
 148 |+         @tparam Allocator Allocator type
```

> Username: vinniefalco  
> Created_at: 2022-01-09 17:16:42 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780808646  

you don't need to document the tparam for a well known concept. Try changing the declaration to say `__Allocator__` and see if docca turns it into a link.


---

## Review 38 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-09 17:16:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/95#pullrequestreview-847283832  

📁 include/boost/url/string.hpp

```diff
 150 |+         @param n Number of chars in the string `dest`
 151 |+ 
 152 |+         @param a Allocator used to create a copy of `dest`
```

> Username: vinniefalco  
> Created_at: 2022-01-09 17:16:57 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780808666  

"The allocator to use"


---

## Review 39 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-09 17:17:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/95#pullrequestreview-847283874  

📁 include/boost/url/string_view.hpp

```diff
  33 | */
  35 |- //typedef boost::string_view string_view;
  34 | typedef boost::core::string_view string_view;
```

> Username: vinniefalco  
> Created_at: 2022-01-09 17:17:22 UTC  
> Url: https://github.com/boostorg/url/pull/95#discussion_r780808717  

yeah if you want this tidying, keep it


---
