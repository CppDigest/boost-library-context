# #3058 Use core::string_view directly instead of alias [Open]

> Username: ssam18  
> Created at: 2025-11-18 01:53:57 UTC  
> Updated at: 2026-01-04 20:23:22 UTC  
> Url: https://github.com/boostorg/beast/pull/3058  

This PR addresses issue #3046 by replacing all uses of the `string_view` alias with `core::string_view` throughout the codebase for improved clarity in both documentation and source code.  
  
## Changes  
  
- Replaced `string_view` with `core::string_view` in all header files (.hpp)  
- Replaced `string_view` with `core::string_view` in all inline implementation files (.ipp)  
- Updated documentation files (.qbk) to use `core::string_view`  
- Replaced `basic_string_view` with `core::basic_string_view` where applicable  
- Retained type aliases in `string_type.hpp` for backward compatibility  
  
## Benefits  
  
- Makes it explicit that Beast uses `boost::core::string_view`  
- Improves code clarity and documentation  
- Easier for users to understand the library's dependencies  
  
## Files Modified  
  
- 48 header and implementation files  
- 4 documentation files  
- Total: 52 files with 410 insertions and 347 deletions  
  
Closes #3046

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-11-18 02:03:26 UTC  
> Updated_at: 2026-01-04 20:23:22 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#issuecomment-3544697160  

An automated preview of the documentation is available at [https://3058.beast.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://3058.beast.prtest.cppalliance.org/libs/beast/doc/html/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2026-01-04 20:23:21 UTC

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-11-18 03:19:36 UTC  
> Updated_at: 2025-11-20 19:50:23 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#issuecomment-3544849251  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/3058?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 93.19%. Comparing base ([`67106eb`](https://app.codecov.io/gh/boostorg/beast/commit/67106ebaab7a644a9bdb5d6e6b0a3f4ad1d0e4f7?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`7188b56`](https://app.codecov.io/gh/boostorg/beast/commit/7188b56256315f29b79015710a14db902cff3216?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/3058/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/3058?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #3058      +/-   ##  
===========================================  
- Coverage    93.22%   93.19%   -0.03%       
===========================================  
  Files          176      176                
  Lines        13703    13703                
===========================================  
- Hits         12774    12770       -4       
- Misses         929      933       +4       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/beast/pull/3058?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [example/doc/http\_examples.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3058?src=pr&el=tree&filepath=example%2Fdoc%2Fhttp_examples.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZXhhbXBsZS9kb2MvaHR0cF9leGFtcGxlcy5ocHA=) | `80.36% <100.00%> (ø)` | |  
| [...ude/boost/beast/\_experimental/test/impl/stream.ipp](https://app.codecov.io/gh/boostorg/beast/pull/3058?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2F_experimental%2Ftest%2Fimpl%2Fstream.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3Rlc3QvaW1wbC9zdHJlYW0uaXBw) | `98.36% <ø> (ø)` | |  
| [include/boost/beast/\_experimental/test/stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3058?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2F_experimental%2Ftest%2Fstream.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3Rlc3Qvc3RyZWFtLmhwcA==) | `75.00% <ø> (ø)` | |  
| [.../boost/beast/core/detail/impl/temporary\_buffer.ipp](https://app.codecov.io/gh/boostorg/beast/pull/3058?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Fdetail%2Fimpl%2Ftemporary_buffer.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9pbXBsL3RlbXBvcmFyeV9idWZmZXIuaXBw) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/detail/string.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3058?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Fdetail%2Fstring.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9zdHJpbmcuaHBw) | `100.00% <100.00%> (ø)` | |  
| [...clude/boost/beast/core/detail/temporary\_buffer.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3058?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Fdetail%2Ftemporary_buffer.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC90ZW1wb3JhcnlfYnVmZmVyLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/impl/string.ipp](https://app.codecov.io/gh/boostorg/beast/pull/3058?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Fimpl%2Fstring.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvc3RyaW5nLmlwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/string\_type.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3058?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Fstring_type.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL3N0cmluZ190eXBlLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/beast/http/basic\_parser.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3058?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fbasic_parser.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `94.11% <ø> (ø)` | |  
| [include/boost/beast/http/chunk\_encode.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3058?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fchunk_encode.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2NodW5rX2VuY29kZS5ocHA=) | `100.00% <ø> (ø)` | |  
| ... and [30 more](https://app.codecov.io/gh/boostorg/beast/pull/3058?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/3058/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/3058?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/3058?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [67106eb...7188b56](https://app.codecov.io/gh/boostorg/beast/pull/3058?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 3

> Username: ashtum  
> Created_at: 2025-11-18 05:17:44 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#issuecomment-3545082818  

Thank you. Could you please check the `example` and `test` directories as well?

---

## Review 4 [Commented]

> Username: ashtum  
> Created_at: 2025-11-18 05:19:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3058#pullrequestreview-3475502056  

📁 replace_string_view.py

```diff
   1 |+ #!/usr/bin/env python3
```

> Username: ashtum  
> Created_at: 2025-11-18 05:19:12 UTC  
> Updated_at: 2025-11-18 05:19:54 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2536341838  

Please exclude `replace_string_view.py` from the committed files.


---

## Comment 5

> Username: vinniefalco  
> Created_at: 2025-11-18 07:30:15 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#issuecomment-3545898054  

This is great!

---

## Comment 6

> Username: ssam18  
> Created_at: 2025-11-18 14:23:18 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#issuecomment-3547853462  

Thank you @ashtum for the review! I've updated the PR to:  
  
1. Process example and test directories - added 58 more files with string_view replacements  
2. Remove replace_string_view.py from the committed files

---

## Review 7 [Commented]

> Username: ashtum  
> Created_at: 2025-11-18 15:24:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3058#pullrequestreview-3478382537  

📁 include/boost/beast/core/string_type.hpp

```diff
  23 | template<class CharT>
  24 | using basic_string_view =
  25 |     boost::core::basic_string_view<CharT>;
```

> Username: ashtum  
> Created_at: 2025-11-18 15:24:47 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2538633843  

There is no need for these type aliases anymore.


---

## Review 8 [Commented]

> Username: ashtum  
> Created_at: 2025-11-18 15:27:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3058#pullrequestreview-3478395940  

📁 .gitignore

```diff
   6 | x64/
   7 | 
   8 |+ replace_string_view.py
```

> Username: ashtum  
> Created_at: 2025-11-18 15:27:58 UTC  
> Updated_at: 2025-11-18 15:29:06 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2538644954  

No need for that. It’s not something that belongs to the project.


---

## Review 9 [Commented]

> Username: ashtum  
> Created_at: 2025-11-18 15:30:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3058#pullrequestreview-3478408158  

📁 doc/qbk/04_http/08_chunked_encoding.qbk

```diff
 181 |             std::uint64_t size,         // Size of the chunk, zero for the last chunk
 182 |-             string_view extensions,     // The chunk-extensions in raw form
 182 |+             core::string_view extensions,     // The chunk-extensions in raw form
```

> Username: ashtum  
> Created_at: 2025-11-18 15:30:37 UTC  
> Updated_at: 2025-11-18 15:32:29 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2538655023  

Please realign all the comments above and below this line. There are more cases of this in the PR, please fix them as well.


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2025-11-18 15:36:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3058#pullrequestreview-3478435859  

📁 doc/qbk/08_design/1_http_message.qbk

```diff
 277 |-     string_view reason() const;
 278 |-     void        reason(string_view);
 277 |+     core::string_view reason() const;
```

> Username: vinniefalco  
> Created_at: 2025-11-18 15:36:52 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2538677729  

this no longer lines up neatly with the other members


---

## Comment 11

> Username: ssam18  
> Created_at: 2025-11-18 15:46:03 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#issuecomment-3548293788  

Thank you for the detailed review! I've addressed all the feedback:  
  
1. Removed the type aliases from string_type.hpp as they're no longer needed since all code now uses core::string_view directly  
2. Removed the .gitignore entry for replace_string_view.py

---

## Review 12 [Commented]

> Username: ashtum  
> Created_at: 2025-11-18 15:50:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3058#pullrequestreview-3478504410  

📁 include/boost/beast/http/parser.hpp

```diff
 240 |             std::uint64_t size,         // Size of the chunk, zero for the last chunk
 241 |-             string_view extensions,     // The chunk-extensions in raw form
 241 |+             core::string_view extensions,     // The chunk-extensions in raw form
```

> Username: ashtum  
> Created_at: 2025-11-18 15:50:48 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2538732040  

Please check for all the formatting and alignment issues. Thank you.


---

## Comment 13

> Username: ssam18  
> Created_at: 2025-11-18 16:25:58 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#issuecomment-3548480843  

Fixed all remaining comment alignment issues.

---

## Comment 14

> Username: ashtum  
> Created_at: 2025-11-18 17:08:45 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#issuecomment-3548690791  

@ssam18 You can use the GitHub Action CI in your own fork to address the compile errors, since it won’t require approval to run each time.

---

## Review 15 [Commented]

> Username: ashtum  
> Created_at: 2025-11-18 19:12:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3058#pullrequestreview-3479355377  

📁 example/doc/http_examples.hpp

```diff
 983 |-         error_code& ev)             // We can set this to indicate an error
 981 |+     [&](std::uint64_t size,               // Size of the chunk, or zero for the last chunk
 982 |+         boost::core::string_view extensions,     // The raw chunk-extensions string. Already validated.
```

> Username: ashtum  
> Created_at: 2025-11-18 19:12:16 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2539376092  

Comment alignment.


---

## Review 16 [Commented]

> Username: ashtum  
> Created_at: 2025-11-18 19:14:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3058#pullrequestreview-3479363652  

📁 test/doc/http_10_custom_parser.cpp

```diff
  92 |+         field f,                          // The known-field enumeration constant
  93 |+         boost::core::string_view name,           // The field name string.
  94 |+         boost::core::string_view value,          // The field value
```

> Username: ashtum  
> Created_at: 2025-11-18 19:14:24 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2539382489  

Comment alignment. Please check the entire PR, there are more cases.

> Username: ssam18  
> Created_at: 2025-11-18 19:26:14 UTC  
> Updated_at: 2025-11-18 19:26:15 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2539415264  

yeah, I missed this as well. Will get it fixed.


---

## Review 17 [Commented]

> Username: ashtum  
> Created_at: 2025-11-18 19:16:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3058#pullrequestreview-3479371461  

📁 test/beast/websocket/read3.cpp

```diff
 101 |             // insert a ping
 102 |-             ws.next_layer().append(string_view(
 102 |+             ws.next_layer().append(boost::core::string_view(
```

> Username: ashtum  
> Created_at: 2025-11-18 19:16:23 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2539388245  

We are already inside the `boost` namespace. What's the reason for using namespace qualifiers?

> Username: ssam18  
> Created_at: 2025-11-18 19:25:39 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2539413738  

hmm. I have habit of using boost namespace by default. Let me fix this one.


---

## Comment 18

> Username: ssam18  
> Created_at: 2025-11-18 23:41:01 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#issuecomment-3549902149  

Why the core::string_view → boost::core::string_view change is necessary?  
  
The issue:  
  
C++ namespace lookup rules mean that when you write core::string_view in a file, the compiler searches for core starting from the current namespace and working outward. This works fine in files already inside namespace boost { ... } because the compiler finds boost::core. But in standalone example and test files that aren't wrapped in the boost namespace, the compiler can't find core at all.  
  
FIX:  
  
Files need to use the fully qualified name boost::core::string_view unless they're already inside the boost namespace. This is why:  
  
Example files (like example/http/server/async/http_server_async.cpp) → Need boost::core::string_view (standalone programs)  
Test/doc files outside boost namespace → Need boost::core::string_view  
Header files inside namespace boost (like example/doc/http_examples.hpp) → Can use core::string_view (already in boost namespace)  
This is standard C++ namespace behavior—you need the full qualification when you're not already in the parent namespace. The changes ensure MSVC (and all other compilers) can properly resolve the type regardless of where it's used.

---

## Review 19 [Commented]

> Username: ashtum  
> Created_at: 2025-11-19 05:20:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3058#pullrequestreview-3480864206  

📁 include/boost/beast/http/impl/verb.ipp

```diff
  20 | 
  21 |- string_view
  21 |+ boost::core::string_view
```

> Username: ashtum  
> Created_at: 2025-11-19 05:20:13 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2540579364  

There are still several places where the `boost::` namespace is used unnecessarily.


---

## Review 20 [Commented]

> Username: ashtum  
> Created_at: 2025-11-19 05:20:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3058#pullrequestreview-3480865328  

📁 doc/qbk/release_notes.qbk

```diff
 260 | * [issue 2363] Remove `BOOST_BEAST_USE_STD_STRING_VIEW`
 261 |- * [issue 2417] use boost::core::string_view. This improves inter-conversion between string_view implementations. Some observable differences for users:
 261 |+ * [issue 2417] use boost::core::string_view. This improves inter-conversion between core::string_view implementations. Some observable differences for users:
```

> Username: ashtum  
> Created_at: 2025-11-19 05:20:53 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2540580381  

This is our release notes please don't modify that.


---

## Review 21 [Commented]

> Username: ashtum  
> Created_at: 2025-11-20 04:59:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3058#pullrequestreview-3485672488  

📁 test/doc/core_1_refresher.cpp

```diff
  35 |         net::const_buffer cb("Hello, world!", 13);
  36 |-         assert(string_view(reinterpret_cast<char const*>(
  36 |+         assert(boost::core::string_view(reinterpret_cast<char const*>(
```

> Username: ashtum  
> Created_at: 2025-11-20 04:59:11 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2544337615  

The `boost` namespace qualifier is used unnecessarily in several places in source files in `test/doc`.

> Username: ssam18  
> Created_at: 2025-11-20 15:57:02 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2546645142  

I tried removing boost namespace qualifier from the test/doc but CI is fails in MSVC. Please take a look at the previous checkins.

> Username: ashtum  
> Created_at: 2025-11-20 16:59:32 UTC  
> Updated_at: 2025-11-20 16:59:33 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2546876460  

Could you please link to that, or copy the related error here?

> Username: ssam18  
> Created_at: 2025-11-20 17:22:11 UTC  
> Updated_at: 2025-11-20 17:22:12 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2546964420  

https://github.com/boostorg/beast/pull/3058/commits/8015b7a5e46b512b36c87a950d768e876cfaec23#diff-f431051c436803c230374f4edc1882f63163c02be74b660c138603a8e89ae465

> Username: ashtum  
> Created_at: 2025-11-20 17:30:10 UTC  
> Updated_at: 2025-11-20 17:30:11 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2546995375  

Sorry, I mean the compiler error that you get.

> Username: ssam18  
> Created_at: 2025-11-20 17:42:43 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2547037680  

yes, that is correct. I tried taking off boost namespace qualifier but CI keeps on failing.

> Username: ashtum  
> Created_at: 2025-11-20 17:46:51 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2547049641  

> yes, that is correct. I tried taking off boost namespace qualifier but CI keeps on failing.  
  
Could you please provide the related compiler error?

> Username: ssam18  
> Created_at: 2025-11-20 18:00:13 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2547088447  

just now I pushed the changes without boost namespace. Let's see if CI is OK with that.


---

## Comment 22

> Username: ssam18  
> Created_at: 2025-11-30 15:04:43 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#issuecomment-3592651333  

@ashtum Please let me know if this PR looks good?

---

## Comment 23

> Username: ashtum  
> Created_at: 2025-11-30 15:49:02 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#issuecomment-3592724787  

> @ashtum Please let me know if this PR looks good?  
  
All files under `test/doc` and `example` should use `core::string_view` rather than `boost::string_view`.  
If needed, add `namespace core = boost::core;` in the correct location, following the style of nearby `using` declarations.  
  
For example:  
```CPP  
namespace beast = boost::beast;          // from <boost/beast.hpp>  
namespace http = beast::http;            // from <boost/beast/http.hpp>  
namespace websocket = beast::websocket;  // from <boost/beast/websocket.hpp>  
namespace net = boost::asio;             // from <boost/asio.hpp>  
namespace ssl = boost::asio::ssl;        // from <boost/asio/ssl.hpp>  
namespace core = boost::core;            // from <boost/core/detail/string_view.hpp>  
using tcp = boost::asio::ip::tcp;        // from <boost/asio/ip/tcp.hpp>  
```  
  
Please review each changed line manually and use your judgment to fix any formatting, clarity, or semantic issues.  
Please run the CI on your branch and address the build failures (you need to activate GHA action for your fork).

---

## Review 24 [Commented]

> Username: ashtum  
> Created_at: 2025-12-02 05:43:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3058#pullrequestreview-3528208221  

📁 example/advanced/server-flex/advanced_server_flex.cpp

```diff
  42 | namespace net = boost::asio;                    // from <boost/asio.hpp>
  43 | namespace ssl = boost::asio::ssl;               // from <boost/asio/ssl.hpp>
  44 |+ namespace core = boost::core;               // from <boost/core/detail/string_view.hpp>
```

> Username: ashtum  
> Created_at: 2025-12-02 05:40:25 UTC  
> Updated_at: 2025-12-02 05:43:11 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2579651414  

Comment should align with upper lines (There are still a few cases of misalignment please fix all of them).


📁 example/http/server/async-local/http_server_async_local.cpp

```diff
  37 | namespace net = boost::asio;                         // from <boost/asio.hpp>
  38 | using stream_protocol = net::local::stream_protocol; // from <boost/asio/local/stream_protocol.hpp>
  39 |+ namespace core = boost::core;                        // from <core/string_view.hpp>
```

> Username: ashtum  
> Created_at: 2025-12-02 05:41:17 UTC  
> Updated_at: 2025-12-02 05:43:11 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2579652940  

`namespace core = boost::core;` should go after `namespace net = boost::asio;` not after the using expression.


📁 test/doc/exemplars.cpp

```diff
  21 | namespace http {
  22 | 
  23 |+ namespace core = boost::core; // <core/string_view.hpp>
```

> Username: ashtum  
> Created_at: 2025-12-02 05:41:53 UTC  
> Updated_at: 2025-12-02 05:43:11 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2579654062  

What is this `<core/string_view.hpp>` ? (There are more of them)


📁 test/doc/http_10_custom_parser.cpp

```diff
 194 |+                                                  // including what is being passed here.
 195 |+                                                  // or zero for the last chunk
 196 |+         core::string_view body,           // The next piece of the chunk body
```

> Username: ashtum  
> Created_at: 2025-12-02 05:42:56 UTC  
> Updated_at: 2025-12-02 05:43:11 UTC  
> Url: https://github.com/boostorg/beast/pull/3058#discussion_r2579655885  

Comment alignment.


---
