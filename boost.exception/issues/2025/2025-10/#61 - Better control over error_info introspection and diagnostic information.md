# #61 - Better control over error_info introspection and diagnostic information [Closed]

> Username: sdebionne  
> Created at: 2025-10-10 12:21:23 UTC  
> Updated at: 2026-02-04 17:17:36 UTC  
> Closed at: 2026-02-04 17:17:36 UTC  
> Url: https://github.com/boostorg/exception/issues/61  

We use Boost.Exception in our project but we lack a way to exercise better control over the diagnostic formatting. This has already been discussed before in a related issue https://github.com/boostorg/exception/issues/55.  
  
In our client/server project, we need to 'serialize' the exceptions (e.g. to JSON) while retaining as much information as possible for the client to display. The client being responsible to format and present the error message.  
  
I managed to hack the library's `diagnostic_information` to return a `boost::json::value` (instead of a `std::string`) but I would prefer a solution that is part of the library.  
  
I know this library is kind of superseded by LEAF, but we are not ready to switch (mostly because of the awkward try catch syntax).  
  
What do you think about introducing a customization point (visitor pattern?) to introspect an exception and its `error_info`s to get diagnostic information in a custom data structure?

---

## Comment 1

> Username: zajo  
> Created at: 2025-10-11 20:56:01 UTC  
> Url: https://github.com/boostorg/exception/issues/61#issuecomment-3393652978  

I'd rather have code to serialize to json. How does your json serialization work?

---

## Comment 2

> Username: sdebionne  
> Created at: 2025-10-12 16:18:20 UTC  
> Url: https://github.com/boostorg/exception/issues/61#issuecomment-3394804910  

I use Boost.JSON. Diagnostic information are collected in a 'boost::json::value'. Serialisation to a string is performed using Boost.JSON's serialize function, but this should be outside the scope of Boost.Exception (so if someone wants to serialise to say CBOR instead, this is still possible).  
I am not sure if Boost.Exception wants to have a dependency on Boost.JSON, so my proposal was to provide a generic visiting mechanism instead.

---

## Comment 3

> Username: zajo  
> Created at: 2025-10-12 22:31:40 UTC  
> Url: https://github.com/boostorg/exception/issues/61#issuecomment-3395425509  

I am not familiar with Boost.JSON, it may be possible to define serialization to JSON where the dependency to Boost.JSON exists only if the code that is serializing to JSON is instantiated. I'd be totally fine if only serialization to JSON is implemented. This could be implemented along the lines of how op<< is used if available.  
  
Either way, if you can sketch up a solution I'll take a look.

---

## Comment 4

> Username: sdebionne  
> Created at: 2025-11-17 21:32:19 UTC  
> Url: https://github.com/boostorg/exception/issues/61#issuecomment-3543924509  

Here are two patches that implement a proof of concept:  
  
[exception.patch](https://github.com/user-attachments/files/23592304/exception.patch)  
[throw_exception.patch](https://github.com/user-attachments/files/23592305/throw_exception.patch)  
  
A new test can be build with `./b2 libs/exception/test//diagnostic_information_visitor_test`. The other tests are broken since I did not manage to isolate the JSON part.

---

## Comment 5

> Username: zajo  
> Created at: 2025-11-24 22:43:09 UTC  
> Url: https://github.com/boostorg/exception/issues/61#issuecomment-3573025682  

Thank you, I'll take a look soon.

---

## Comment 6

> Username: zajo  
> Created at: 2025-11-27 19:31:14 UTC  
> Url: https://github.com/boostorg/exception/issues/61#issuecomment-3587106981  

It seems like the .patch files you posted are corrupt. Could you check please?

---

## Comment 7

> Username: sdebionne  
> Created at: 2025-12-03 20:22:27 UTC  
> Url: https://github.com/boostorg/exception/issues/61#issuecomment-3608689765  

Sorry about that, I ended up pushing my modifications in my forks instead:  
  
https://github.com/boostorg/exception/compare/develop...sdebionne:exception:diagnostic-information-json?expand=1  
  
https://github.com/boostorg/throw_exception/compare/develop...sdebionne:throw_exception:diagnostic-information-json?expand=1

---

## Comment 8

> Username: zajo  
> Created at: 2026-01-11 18:43:09 UTC  
> Url: https://github.com/boostorg/exception/issues/61#issuecomment-3735391434  

I have not forgotten about this. I will implement it soon, I have a good approach.

---

## Comment 9

> Username: zajo  
> Created at: 2026-01-12 18:58:34 UTC  
> Url: https://github.com/boostorg/exception/issues/61#issuecomment-3740036794  

Can you take a look in the feature/serialization branch and tell me if this works for you? There is a unit test for serializing error objects using nlohmann/json. Note, you'll need to switch both the throw_exception and the exception repos to feature/serialization.

---

## Comment 10

> Username: sdebionne  
> Created at: 2026-01-19 17:37:38 UTC  
> Url: https://github.com/boostorg/exception/issues/61#issuecomment-3769505016  

Thank you so much for that feature and congratulation for the design. I have managed to adapt it to Boost.JSON. Enclosed are the writer and test files, if you want to add them the examples/test, feel free.  
  
[boost_json_test.zip](https://github.com/user-attachments/files/24721319/boost_json_test.zip)

---

## Comment 11

> Username: zajo  
> Created at: 2026-01-20 02:35:54 UTC  
> Url: https://github.com/boostorg/exception/issues/61#issuecomment-3770755209  

Thanks. I'll tidy it up a bit and it'll go out with the next Boost release. I'll close this when I merge to master.
