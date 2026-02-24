# #153 fix unwanted virtual call bypass during destruction [Closed]

> Username: adrianimboden  
> Created at: 2020-03-27 17:41:35 UTC  
> Updated at: 2020-05-20 14:05:00 UTC  
> Closed at: 2020-05-20 14:05:00 UTC  
> Url: https://github.com/boostorg/process/pull/153  

I just upgraded to the new boost version and clang-tidy detected this error.  
  
These changes fix the problem/warning. I don't think that there is a case where the functions really are going to be overriden in a derived class.  
  
```  
/usr/include/boost/process/pipe.hpp:127:13: error: Call to virtual method 'basic_pipebuf::overflow' during destruction bypasses virtual dispatch [clang-analyzer-optin.cplusplus.VirtualCall,-warnings-as-errors]  
            overflow(Traits::eof());  
            ^  
/tmp/reproducer.cpp:44:12: note: Calling implicit destructor for 'basic_opstream<char, std::__1::char_traits<char>>'  
    struct Data {  
           ^  
/tmp/reproducer.cpp:44:12: note: Calling '~basic_pipebuf'  
/usr/include/boost/process/pipe.hpp:126:9: note: Taking true branch  
        if (is_open())  
        ^  
/usr/include/boost/process/pipe.hpp:127:13: note: Call to virtual method 'basic_pipebuf::overflow' during destruction bypasses virtual dispatch  
            overflow(Traits::eof());  
            ^  
/usr/include/boost/process/pipe.hpp:184:12: error: Call to virtual method 'basic_pipebuf::sync' during destruction bypasses virtual dispatch [clang-analyzer-optin.cplusplus.VirtualCall,-warnings-as-errors]  
           this->sync();  
           ^  
/tmp/reproducer.cpp:44:12: note: Calling implicit destructor for 'basic_opstream<char, std::__1::char_traits<char>>'  
    struct Data {  
           ^  
/tmp/reproducer.cpp:44:12: note: Calling '~basic_pipebuf'  
/usr/include/boost/process/pipe.hpp:126:9: note: Taking true branch  
        if (is_open())  
        ^  
/usr/include/boost/process/pipe.hpp:127:13: note: Calling 'basic_pipebuf::overflow'  
            overflow(Traits::eof());  
            ^  
/usr/include/boost/process/pipe.hpp:165:13: note: Left side of '&&' is true  
        if (_pipe.is_open() && (ch != traits_type::eof()))  
            ^  
/usr/include/boost/process/pipe.hpp:165:9: note: Taking false branch  
        if (_pipe.is_open() && (ch != traits_type::eof()))  
        ^  
/usr/include/boost/process/pipe.hpp:183:14: note: Taking true branch  
        else if (ch == traits_type::eof())  
             ^  
/usr/include/boost/process/pipe.hpp:184:12: note: Call to virtual method 'basic_pipebuf::sync' during destruction bypasses virtual dispatch  
           this->sync();  
```

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-03-27 18:00:17 UTC  
> Updated_at: 2020-03-27 18:10:27 UTC  
> Url: https://github.com/boostorg/process/pull/153#issuecomment-605169152  

# [Codecov](https://codecov.io/gh/boostorg/process/pull/153?src=pr&el=h1) Report  
> Merging [#153](https://codecov.io/gh/boostorg/process/pull/153?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/process/commit/668579ed6f7e3ed8fa907b31c2ca2b091df5b8e8&el=desc) will **decrease** coverage by `6.65%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/process/pull/153/graphs/tree.svg?width=650&height=150&src=pr&token=AhunMqTSpA)](https://codecov.io/gh/boostorg/process/pull/153?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #153      +/-   ##  
===========================================  
- Coverage    89.14%   82.48%   -6.66%       
===========================================  
  Files          110      111       +1       
  Lines         3132     3009     -123       
===========================================  
- Hits          2792     2482     -310       
- Misses         340      527     +187       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/process/pull/153?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/process/pipe.hpp](https://codecov.io/gh/boostorg/process/pull/153/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL3BpcGUuaHBw) | `73.01% <100.00%> (-4.26%)` | :arrow_down: |  
| [include/boost/process/shell.hpp](https://codecov.io/gh/boostorg/process/pull/153/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL3NoZWxsLmhwcA==) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/exception.hpp](https://codecov.io/gh/boostorg/process/pull/153/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2V4Y2VwdGlvbi5ocHA=) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/detail/posix/shell\_path.hpp](https://codecov.io/gh/boostorg/process/pull/153/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9zaGVsbF9wYXRoLmhwcA==) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [...nclude/boost/process/detail/posix/group\_handle.hpp](https://codecov.io/gh/boostorg/process/pull/153/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9ncm91cF9oYW5kbGUuaHBw) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/group.hpp](https://codecov.io/gh/boostorg/process/pull/153/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2dyb3VwLmhwcA==) | `15.38% <0.00%> (-78.74%)` | :arrow_down: |  
| [include/boost/process/detail/config.hpp](https://codecov.io/gh/boostorg/process/pull/153/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9jb25maWcuaHBw) | `0.00% <0.00%> (-61.54%)` | :arrow_down: |  
| [test/run\_exe\_path.cpp](https://codecov.io/gh/boostorg/process/pull/153/diff?src=pr&el=tree#diff-dGVzdC9ydW5fZXhlX3BhdGguY3Bw) | `60.00% <0.00%> (-40.00%)` | :arrow_down: |  
| [include/boost/process/error.hpp](https://codecov.io/gh/boostorg/process/pull/153/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2Vycm9yLmhwcA==) | `60.00% <0.00%> (-40.00%)` | :arrow_down: |  
| [test/search\_path.cpp](https://codecov.io/gh/boostorg/process/pull/153/diff?src=pr&el=tree#diff-dGVzdC9zZWFyY2hfcGF0aC5jcHA=) | `63.63% <0.00%> (-36.37%)` | :arrow_down: |  
| ... and [80 more](https://codecov.io/gh/boostorg/process/pull/153/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/process/pull/153?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/process/pull/153?src=pr&el=footer). Last update [668579e...8ea972b](https://codecov.io/gh/boostorg/process/pull/153?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2020-05-20 12:42:24 UTC  
> Url: https://github.com/boostorg/process/pull/153#issuecomment-631449117  

I think the correct solution would be to explicitly pick the verison in the destructor, instead of making the functions final

---
