# #194 - TSAN tests for kqueue implementations [Open]

> Username: anarthal  
> Created at: 2023-12-20 12:10:56 UTC  
> Updated at: 2023-12-20 12:10:57 UTC  
> Url: https://github.com/boostorg/mysql/issues/194  

TSAN tests were disabled due to a reported race condition in Asio for systems using kqueue (like OSX). Same stacktrace as https://github.com/chriskohlhoff/asio/issues/641 (except we're not mixing sync and async).
