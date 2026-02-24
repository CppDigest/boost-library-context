# #17 Fix the documentation redirect [Closed]

> Username: danieljames  
> Created at: 2017-04-27 18:30:52 UTC  
> Updated at: 2017-04-27 22:37:57 UTC  
> Closed at: 2017-04-27 19:39:40 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/17  

Currently the documentation is at:  
  
http://www.boost.org/doc/libs/develop/doc/html/boost_stacktrace.html  
  
The filename is generated from the document's id in quickbook. Since you don't have one, it generates an id from the title "Boost.Stacktrace", while is why the file is called `boost_stacktrace.html`. So either need to change the redirect file to go there, or add '[id stacktrace]' to the library details in your quickbook documentation.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2017-04-27 19:39:40 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/17#issuecomment-297817931  

Thanks! I've added the '[id stacktrace]' to docs.

---

## Comment 2

> Username: coveralls  
> Created_at: 2017-04-27 21:45:14 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/17#issuecomment-297848010  

[![Coverage Status](https://coveralls.io/builds/11278696/badge)](https://coveralls.io/builds/11278696)  
  
Coverage remained the same at 93.237% when pulling **3c76f8fab64044a148d7fc3344a83faefd0b01ee on danieljames-patch-1** into **a562d4cb956bc16b30e882de7527664f7b070507 on master**.

---

## Comment 3

> Username: coveralls  
> Created_at: 2017-04-27 21:45:14 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/17#issuecomment-297848011  

[![Coverage Status](https://coveralls.io/builds/11278696/badge)](https://coveralls.io/builds/11278696)  
  
Coverage remained the same at 93.237% when pulling **3c76f8fab64044a148d7fc3344a83faefd0b01ee on danieljames-patch-1** into **a562d4cb956bc16b30e882de7527664f7b070507 on master**.

---

## Comment 4

> Username: coveralls  
> Created_at: 2017-04-27 21:45:14 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/17#issuecomment-297848012  

[![Coverage Status](https://coveralls.io/builds/11278696/badge)](https://coveralls.io/builds/11278696)  
  
Coverage remained the same at 93.237% when pulling **3c76f8fab64044a148d7fc3344a83faefd0b01ee on danieljames-patch-1** into **a562d4cb956bc16b30e882de7527664f7b070507 on master**.

---
