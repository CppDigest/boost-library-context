# #158 fix missing header and inverted ifdef [Closed]

> Username: HDembinski  
> Created at: 2019-05-12 18:31:25 UTC  
> Updated at: 2019-06-07 20:48:25 UTC  
> Closed at: 2019-06-07 08:59:40 UTC  
> Url: https://github.com/boostorg/serialization/pull/158  

@glenfe asked me to support BOOST_NO_EXCEPTIONS in Boost.Histogram and thus I found this issue. The serialization version of throw_exception calls ::boost::throw_exception but didn't include the header <boost/throw_exception.hpp>, so it only worked by chance if that header was already included.

---

## Comment 1

> Username: HDembinski  
> Created_at: 2019-06-07 08:43:54 UTC  
> Url: https://github.com/boostorg/serialization/pull/158#issuecomment-499806847  

@robertramey Could you please have a look at the failing test? I looked into the log and I can't find out what's wrong with. It would be really nice if you could accept this PR. I am using Boost.Serialization in Boost.Histogram and I cannot test the serialization parts with exceptions turned off because of this bug.

---

## Comment 2

> Username: HDembinski  
> Created_at: 2019-06-07 08:59:40 UTC  
> Url: https://github.com/boostorg/serialization/pull/158#issuecomment-499811550  

On second look, I am not sure whether my patch is correct.

---

## Comment 3

> Username: robertramey  
> Created_at: 2019-06-07 15:45:42 UTC  
> Url: https://github.com/boostorg/serialization/pull/158#issuecomment-499936916  

FYI - My personal experience is that running tests on one's own system is much better than depending on CI tests.  For one things, one can debug the stuff on one's own system.  CI works best when you know that things work on your own system but you need confirmation that it works on other systems that you can test on.

---

## Comment 4

> Username: HDembinski  
> Created_at: 2019-06-07 20:48:25 UTC  
> Url: https://github.com/boostorg/serialization/pull/158#issuecomment-500033650  

I do both. The CI tests make sure that I am not breaking things on Windows (I develop on Linux and OSX) and under special conditions that I do not test regularly, such as -fno-exceptions.

---
