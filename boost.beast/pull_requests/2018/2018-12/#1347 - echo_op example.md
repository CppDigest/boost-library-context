# #1347 echo_op example [Closed]

> Username: octobanana  
> Created at: 2018-12-03 23:07:00 UTC  
> Updated at: 2018-12-07 00:44:34 UTC  
> Closed at: 2018-12-07 00:44:34 UTC  
> Url: https://github.com/boostorg/beast/pull/1347  

Set the `reuse_address` socket option to match the style of the other examples.  
  
---  
  
Add the ability to set the listening address and port through command line arguments. Print out usage and return error exit status on invalid or missing arguments.  
  
---  
  
Currently, the __echo-op__ example uses the carriage return `\r` delimiter for `async_read_until`. Running the server and sending a message to it with curl or netcat:  
```sh  
printf 'Hello, World!\r\n' | nc 127.0.0.1 8080  
```  
leads to some potentially confusing behaviour. The `async_read_until` function reads up to and including the first `\r`. When the message is received by the client and printed to stdout, the trailing `\r` ends up moving the cursor back to the start of the line, resulting in the prompt overwriting the message. It appears as if nothing has happened.  
  
If `async_read_until` uses the newline `\n` as the delimiter, the echoed output will not be overwritten, regardless of whether the line ends in `\n` or `\r\n`.  
  
---  
  
Had a go at adding a synchronous echo op function #1275.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-12-03 23:30:42 UTC  
> Url: https://github.com/boostorg/beast/pull/1347#issuecomment-443910876  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1347?src=pr&el=h1) Report  
> Merging [#1347](https://codecov.io/gh/boostorg/beast/pull/1347?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/397c388e18415798847cbcc574ad8ff95633a233?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1347/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1347?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1347      +/-   ##  
===========================================  
+ Coverage    92.06%   92.07%   +<.01%       
===========================================  
  Files          124      124                
  Lines        11526    11526                
===========================================  
+ Hits         10611    10612       +1       
+ Misses         915      914       -1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1347?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/impl/buffers\_prefix.ipp](https://codecov.io/gh/boostorg/beast/pull/1347/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyc19wcmVmaXguaXBw) | `92.22% <0%> (+1.11%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1347?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1347?src=pr&el=footer). Last update [397c388...fc76c42](https://codecov.io/gh/boostorg/beast/pull/1347?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2018-12-03 23:52:03 UTC  
> Url: https://github.com/boostorg/beast/pull/1347#issuecomment-443915355  

The sync echo-op needs exposition and documentation...

---

## Comment 3

> Username: octobanana  
> Created_at: 2018-12-05 05:25:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1347#issuecomment-444363386  

It does, I'll add some documentation to it.

---
