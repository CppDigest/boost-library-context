# #2592 File body improve [Merged]

> Username: klemens-morgenstern  
> Created at: 2022-12-17 10:07:49 UTC  
> Updated at: 2023-02-04 18:57:42 UTC  
> Merged at: 2023-02-04 18:57:41 UTC  
> Closed at: 2023-02-04 18:57:41 UTC  
> Url: https://github.com/boostorg/beast/pull/2592  

- commit 1 is a config option, that's surely useful  
- commit 2 bugfix  
- commit 3 a very useful feature.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-12-17 10:16:46 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#issuecomment-1356153763  

An automated preview of the documentation is available at [https://2592.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2592.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-12-17 10:33:40 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#issuecomment-1356160565  

![pullrequest](https://2592.beast.tracing.cppalliance.org/pullrequest-5827b98b.png)  
Timeline tracing charts: [https://2592.beast.tracing.cppalliance.org/index.html](https://2592.beast.tracing.cppalliance.org/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-12-18 07:11:46 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#issuecomment-1356712097  

An automated preview of the documentation is available at [https://2592.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2592.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-12-18 07:32:05 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#issuecomment-1356721906  

![pullrequest](https://2592.beast.tracing.cppalliance.org/pullrequest-a88c5196.png)  
Timeline tracing charts: [https://2592.beast.tracing.cppalliance.org/index.html](https://2592.beast.tracing.cppalliance.org/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-12-19 03:31:54 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#issuecomment-1357041653  

An automated preview of the documentation is available at [https://2592.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2592.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Review 6 [Commented]

> Username: malixi94  
> Created_at: 2022-12-19 04:50:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2592#pullrequestreview-1222279998  

📁 include/boost/beast/http/basic_file_body.hpp

```diff
 236 |+ seek(std::uint64_t offset, error_code& ec)
 237 |+ {
 238 |+     file_.seek(offset, ec);
```

> Username: malixi94  
> Created_at: 2022-12-19 04:37:53 UTC  
> Updated_at: 2022-12-19 04:50:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#discussion_r1051785920  

In my testing of Beast with my own implementation of file serving and seeking from the system, reading the last part of a file will not only set the eof bit (which seeking automatically clears) but also the fail bit. So before seeking here I think you might want to test for failure / error and if so, clear the fail bit condition.  
  
Though this is only if you want to make the file class here be more stateful with general seeking.  But if you were to only allow seeking the file once (say through an overload of the reset method and getting rid of the seek method) then you don't need to test for this condition. If the user wants to seek the file again then they need to reset it or discard the current object and instantiate a new file class object.

---

📁 include/boost/beast/http/basic_file_body.hpp

```diff
 138 |+     limit_size(std::uint64_t limit, error_code & ec)
 139 |+     {
 140 |+         std::uint64_t actual_size = file_.size(ec);
```

> Username: malixi94  
> Created_at: 2022-12-19 04:48:03 UTC  
> Updated_at: 2022-12-19 04:50:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#discussion_r1051790218  

By this point in the state of the file object, doesn't the member `file_size_` already have the proper size info? If so why do you need to calculate it again with this automatic variable? If my hypothesis is correct, this method could be a one liner:  
`return file_size_ = (std::min)(file_size_, limit);`

> Username: klemens-morgenstern  
> Created_at: 2022-12-19 04:57:44 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#discussion_r1051798623  

This is so you can write only a limited amount of the file, i.e. an incomplete chunk

> Username: vinniefalco  
> Created_at: 2023-01-23 13:52:51 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#discussion_r1084083370  

To write a range, ok

> Username: vinniefalco  
> Created_at: 2023-01-23 13:53:45 UTC  
> Updated_at: 2023-01-23 13:53:46 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#discussion_r1084084377  

This is weird interface though 'limit size'.. Why not just a `set_range` for both the position and length?

> Username: klemens-morgenstern  
> Created_at: 2023-01-24 01:58:16 UTC  
> Updated_at: 2023-01-24 01:58:17 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#discussion_r1084754260  

Because you might set the limit when you've already read parts of it, i.e. moved the cursor. So you limit the size to 2MB after 1MB is read already. the set_range would reset this.

> Username: vinniefalco  
> Created_at: 2023-01-24 20:44:38 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#discussion_r1085901965  

The precondition for `set_range` should be that no reading has occurred yet


---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-12-21 03:36:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#issuecomment-1360813802  

An automated preview of the documentation is available at [https://2592.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2592.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 8

> Username: codecov[bot]  
> Created_at: 2022-12-21 04:43:25 UTC  
> Updated_at: 2023-02-04 17:14:19 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#issuecomment-1360851517  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2592?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2592](https://codecov.io/gh/boostorg/beast/pull/2592?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (12fba80) into [develop](https://codecov.io/gh/boostorg/beast/commit/99bceb5bff341be69a54fb95bd0befd70c47000c?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (99bceb5) will **increase** coverage by `1.79%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head 12fba80 differs from pull request most recent head 20ed03b. Consider uploading reports for the commit 20ed03b to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2592/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2592?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2592      +/-   ##  
===========================================  
+ Coverage    92.94%   94.73%   +1.79%       
===========================================  
  Files          177      154      -23       
  Lines        13635    12062    -1573       
===========================================  
- Hits         12673    11427    -1246       
+ Misses         962      635     -327       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2592?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/basic\_file\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/2592?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2Jhc2ljX2ZpbGVfYm9keS5ocHA=) | `84.26% <100.00%> (+2.45%)` | :arrow_up: |  
| [include/boost/beast/http/empty\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/2592?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2VtcHR5X2JvZHkuaHBw) | `71.42% <0.00%> (-14.29%)` | :arrow_down: |  
| [include/boost/beast/http/parser.hpp](https://codecov.io/gh/boostorg/beast/pull/2592?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3BhcnNlci5ocHA=) | `81.96% <0.00%> (-4.99%)` | :arrow_down: |  
| [include/boost/beast/websocket/detail/prng.ipp](https://codecov.io/gh/boostorg/beast/pull/2592?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL3BybmcuaXBw) | `96.77% <0.00%> (-3.23%)` | :arrow_down: |  
| [include/boost/beast/http/impl/field.ipp](https://codecov.io/gh/boostorg/beast/pull/2592?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZmllbGQuaXBw) | `95.29% <0.00%> (-2.36%)` | :arrow_down: |  
| [include/boost/beast/http/buffer\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/2592?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2J1ZmZlcl9ib2R5LmhwcA==) | `86.36% <0.00%> (-2.28%)` | :arrow_down: |  
| [include/boost/beast/core/detail/bind\_handler.hpp](https://codecov.io/gh/boostorg/beast/pull/2592?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9iaW5kX2hhbmRsZXIuaHBw) | `98.07% <0.00%> (-1.93%)` | :arrow_down: |  
| [include/boost/beast/http/impl/basic\_parser.hpp](https://codecov.io/gh/boostorg/beast/pull/2592?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmhwcA==) | `65.38% <0.00%> (-1.29%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/2592?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9lcnJvci5pcHA=) | `87.50% <0.00%> (-0.84%)` | :arrow_down: |  
| ... and [78 more](https://codecov.io/gh/boostorg/beast/pull/2592?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2592?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2592?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [99bceb5...20ed03b](https://codecov.io/gh/boostorg/beast/pull/2592?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 9

> Username: klemens-morgenstern  
> Created_at: 2022-12-21 06:47:38 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#issuecomment-1360919520  

@vinniefalco I think this is ready. It'll allow partial transmission of files, but I think it'll need your approval.

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2022-12-21 06:56:58 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#issuecomment-1360925585  

An automated preview of the documentation is available at [https://2592.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2592.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2023-01-23 13:41:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2592#pullrequestreview-1265614242  

📁 include/boost/beast/core/detail/config.hpp

```diff
 115 | 
 116 |+ #ifndef BOOST_BEAST_FILE_BUFFER_SIZE
 117 |+ #define BOOST_BEAST_FILE_BUFFER_SIZE 4096
```

> Username: vinniefalco  
> Created_at: 2023-01-23 13:41:14 UTC  
> Updated_at: 2023-01-23 13:41:15 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#discussion_r1084071007  

This is more ugly than my ex but I guess its better than the alternative, which is nothing. That number should probably be higher, no?

> Username: klemens-morgenstern  
> Created_at: 2023-01-23 14:16:54 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#discussion_r1084111364  

4096 is the previous default and it's asio's default, too.


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2023-01-23 13:51:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2592#pullrequestreview-1265631279  

📁 include/boost/beast/http/basic_file_body.hpp

```diff
 241 |+         file_size_ = file_.size(ec);
 242 |+ 
 243 |+     // Consider the offset
```

> Username: vinniefalco  
> Created_at: 2023-01-23 13:51:54 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#discussion_r1084082333  

These comments add no value... lol


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2023-01-23 18:08:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2592#pullrequestreview-1266090533  

📁 doc/qbk/03_core/8_conf_macros.qbk

```diff
  38 |+ [
  39 |+     [
  40 |+         BOOST_BEAST_FILE_BUFFER_SIZE
```

> Username: vinniefalco  
> Created_at: 2023-01-23 18:08:19 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#discussion_r1084385444  

This needs the backticks to make it use the monospaced font?


---

## Comment 14

> Username: vinniefalco  
> Created_at: 2023-02-02 15:30:12 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#issuecomment-1413928816  

This is better yes

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2023-02-02 15:36:58 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#issuecomment-1413939537  

An automated preview of the documentation is available at [https://2592.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2592.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2023-02-02 16:00:09 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#issuecomment-1413976195  

![pullrequest](https://2592.beast.tracing.cppalliance.org/pullrequest-bcd7df80.png)  
Timeline tracing charts: [https://2592.beast.tracing.cppalliance.org/index.html](https://2592.beast.tracing.cppalliance.org/index.html)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2023-02-04 17:21:51 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#issuecomment-1416805246  

An automated preview of the documentation is available at [https://2592.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2592.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2023-02-04 17:44:59 UTC  
> Url: https://github.com/boostorg/beast/pull/2592#issuecomment-1416810249  

![pullrequest](https://2592.beast.tracing.cppalliance.org/pullrequest-59193093.png)  
Timeline tracing charts: [https://2592.beast.tracing.cppalliance.org/index.html](https://2592.beast.tracing.cppalliance.org/index.html)

---
