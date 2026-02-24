# #2670 - http_client_async_ssl_system_executor.cpp example compile issues [Closed]

> Username: jshanaj  
> Created at: 2023-04-19 13:48:49 UTC  
> Updated at: 2024-02-15 05:32:34 UTC  
> Closed at: 2024-02-15 05:32:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2670  

I have tried a few versions of boost with it's included Beast and am having trouble compileing the asyc with system executer.  
  
The errors with 1.76 were excessive centered around the use of placement_new so I switched to 1.82  
The problem I see is that the tcp::resolver in this example cannot be constructed with the strand.  
  
VS2019 using V142 toolchain with /permissive and c++latest  
  
Did the example not get migrated or did I miss something obvious.  
  
error C2664: 'boost::asio::ip::basic_resolver<boost::asio::ip::tcp,boost::asio::any_io_executor>::basic_resolver(const boost::asio::any_io_executor &)': cannot convert argument 1 from 'boost::asio::strand<boost::asio::system_executor>' to 'const boost::asio::any_io_executor &'  
... Reason: cannot convert from 'boost::asio::strand<boost::asio::system_executor>' to 'const boost::asio::any_io_executor'  
... No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
... see declaration of 'boost::asio::ip::basic_resolver<boost::asio::ip::tcp,boost::asio::any_io_executor>::basic_resolver'  
  
I tried changing to io_any_executer but that cascaded to more errors.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-04-19 13:58:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2670#issuecomment-1514786928  

I would not use the system executor. Does the Beast example compile without changes?

---

## Comment 2

> Username: jshanaj  
> Created at: 2023-04-19 14:01:20 UTC  
> Updated at: 2023-04-19 14:02:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2670#issuecomment-1514791135  

No, The construction of the resolver gives the error posted.  
(er. the compilation of the constructor for the session indicates the ctor for the resolver cannot convert the strand argument)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-04-19 14:21:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2670#issuecomment-1514824970  

You need to choose the proper type for the resolver. Use a `basic_resolver` and manually enter the correct template parameters to match the type of executor (strand in this case). If you stare at the Asio documentation for long enough, it will become apparent. If this fails, print a copy of the Asio documentation and put it under your pillow at night, and each morning you will know a little bit more. Eventually you will have the answer.

---

## Comment 4

> Username: jshanaj  
> Created at: 2023-04-19 14:25:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2670#issuecomment-1514834781  

Thanks. I got sidetracked when the provided example in 2 versions failed to build. Is the System_Executer deprecated in favor of any_io_executer?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2023-04-19 14:28:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2670#issuecomment-1514840658  

Well `system_executor` and `any_ioexecutor` are two different things.  
  
The "system executor" is an executor which always posts submitted work to the system thread pool. What is that you ask? Well this is in theory some optimized operating-system queue which will give vague performance gains (for example using Apple's Grand Central Dispatch on OSX). In practice, it didn't work out too well and nowadays we just avoid it.  
  
While `any_io_executor` is actually a type-erasing wrapper that can hold any executor. This is useful to cut down on template spam.  
  
So for example, an instance of `any_io_executor` can _hold_ an instance of `system_executor` inside it (type-erasure). Or it can hold an `io_context::executor_type`. It can hold any executor.  
  
Asio made a change to all of its I/O objects to add a "default executor" parameter and default that to `any_io_executor` which in theory should not break anything. But it did change the ABI/API of things and that is why you are having some compile troubles.  
  
Note, this has nothing to do with Beast (but we're happy to answer Asio questions).

---

## Comment 6

> Username: jshanaj  
> Created at: 2023-04-19 14:33:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2670#issuecomment-1514848116  

Thanks for that detail. Great explanation. I have been using boost for years, even asio+curl, but have just started looking at Beast for a new project.

---

## Comment 7

> Username: jshanaj  
> Created at: 2023-04-19 19:51:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2670#issuecomment-1515292101  

Giving up for now, Too cryptic. I will rewatch the slushie and the CPPCon 2018  videos and play with some minimal examples (i was trying to add to existing project)

---

## Comment 8

> Username: jshanaj  
> Created at: 2023-04-22 00:08:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2670#issuecomment-1518435632  

Problem Solved.   
   For those that may encounter this edge case. ASIO will not build if c++latest is selected in Visual Studio 2019/2022 without disabling Concepts.  Beast must have Concepts enabled. Set language to C++20 and remove BOOST_ASIO_DISABLE_CONCEPTS if it is set in a vsprops file :-)

---

## Comment 9

> Username: vinniefalco  
> Created at: 2023-04-23 17:31:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2670#issuecomment-1519116865  

Ouch...

---

## Comment 10

> Username: jshanaj  
> Created at: 2023-04-23 23:23:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2670#issuecomment-1519193408  

What versions of openssl is Beast compatible with. To remain compatible with current code I am using boost 1.76 and our 1.1.1.k is missing some headers beast is asking for. I updated to openssl 3.1.0 but the Deprecated functions are giving me errors.

---

## Comment 11

> Username: ashtum  
> Created at: 2024-02-08 09:35:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2670#issuecomment-1933685740  

@jshanaj Boost.Beast doesn't use the OpenSSL interface directly; instead, it utilizes Asio's tls_stream and other related types. So, you should check the compatibility of OpenSSL versions in Asio. Usually, the quickest way is to look for related information in Asio's [Revision History](https://www.boost.org/doc/libs/1_84_0/doc/html/boost_asio/history.html). As you can see, there have been a lot of changes since Boost 1.76 for better compatibility with OpenSSL 3.
