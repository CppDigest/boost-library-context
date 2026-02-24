# #197 - Feature Request: BOOST_TEST_CONTEXT that doesn't require introducing a new scope with braces [Closed]

> Username: dudamoos  
> Created at: 2019-01-22 04:05:51 UTC  
> Updated at: 2019-02-18 21:21:16 UTC  
> Closed at: 2019-02-18 21:20:51 UTC  
> Url: https://github.com/boostorg/test/issues/197  

I've been using Boost.Test for a project at work, and one thing I found annoying is that BOOST_TEST_CONTEXT requires you to create a new scope using braces, and the context information will only live as long as that new scope. What I originally though it would do (and what I really want it to do) is to make the additional context information last for the remainder of the enclosing scope. This allows me to slowly build context information as my test progresses without requiring excessive indentation.  
  
My current workaround is to use a define as below (notice the declaration is no longer in an "if" condition):  
```c++  
#define MY_BOOST_TEST_CONTEXT( context_descr )                                                  \  
    ::boost::test_tools::tt_detail::context_frame BOOST_JOIN( context_frame_, __LINE__ ) =      \  
        ::boost::test_tools::tt_detail::context_frame( BOOST_TEST_LAZY_MSG( context_descr ) )  
```  
  
This allows me to do this:  
```c++  
for (int i = 8; i < 24; ++i) {  
    MY_BOOST_TEST_CONTEXT("corrupting CRC bit " << i);  
    // ...  
}  
```  
  
Instead of this:  
```c++  
for (int i = 8; i < 24; ++i) {  
    BOOST_TEST_CONTEXT("corrupting CRC bit " << i) {  
        // ...  
    }  
}  
```  
  
This particular example may not look like much, but after 3 or 4 additions to the context as my test progresses the indentation starts to get excessive.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-02-08 02:25:07 UTC  
> Url: https://github.com/boostorg/test/issues/197#issuecomment-461668977  

I may have better for you: making the context variadic, like this:  
  
```  
for (int i = 8; i < 24; ++i) {  
    BOOST_TEST_CONTEXT("With level " << i, "Random value=" << some other value){  
  
    }  
}  
```  
  
Would that work for you? You can try it on the branch `topic/GH-197-plural-context-in-single-scope`

---

## Comment 2

> Username: dudamoos  
> Created at: 2019-02-08 02:52:57 UTC  
> Updated at: 2019-02-08 02:59:32 UTC  
> Url: https://github.com/boostorg/test/issues/197#issuecomment-461673456  

Unfortunately that doesn't actually do what I want it to. I need to build the context up as I generate data I want included in it so I can debug test failures. What I'm doing is something like this:  
```c++  
std::vector<uint8_t> data = ...; // generate data  
MY_BOOST_TEST_CONTEXT("data      = " << data);  
session->SendData(data, ... /* packet header fields */);  
  
// RequirePacket() reads the data from the stream including the length header.  
// It also does a few other checks to make sure the data isn't garbage.  
const std::vector<uint32_t> packet = RequirePacket(numWords);  
MY_BOOST_TEST_CONTEXT("packet    = " << packet);  
// ... Do some other checks on the packet. ...  
WriteSingleWord(TESTING_ACK); // Acknowledge the packet.  
  
std::vector<uint8_t> parseData;  
// ParsePacket() returns false if the packet was badly formatted or failed integrity checks.  
BOOST_TEST(ParsePacket(packet, parseData, ... /* output parameters to receive header fields */));  
MY_BOOST_TEST_CONTEXT("parseData = " << parseData);  
  
// With all of the above context I can manually inspect the original data, sent packet, and parsed  
// data in case this comparison fails.  
BOOST_TEST(data == parseData, boost::test_tools::per_element());  
```  
  
I also use it to remove an extra level of indentation from the entire body of `Require*()` functions (like `RequirePacket()` above) like so:  
```c++  
#define RequirePacket(numWords) \  
    Internal_RequirePacket((numWords), BOOST_TEST_L(__FILE__), static_cast<std::size_t>(__LINE__))  
std::vector<uint32_t>  
Internal_RequirePacket(unsigned numWords, boost::unit_test::const_string file, std::size_t line) {  
    MY_BOOST_TEST_CONTEXT("RequirePacket() call-site: " << file << "(" << line << ")");  
  
    // Code to read packet from stream and validate length and some other things.  
    ...  
}  
```

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2019-02-08 10:27:24 UTC  
> Url: https://github.com/boostorg/test/issues/197#issuecomment-461758266  

I guess you have considered the [assertion bound context](https://www.boost.org/doc/libs/1_69_0/libs/test/doc/html/boost_test/test_output/test_tools_support_for_logging/contexts.html#boost_test.test_output.test_tools_support_for_logging.contexts.assertion_bound_context) ?  
  
If a context needs to span several assertions, we need a scope, otherwise it will confuse users. If a context is associated to only one assertion, then `BOOST_TEST_INFO` is doing just fine.  
  
What I did here is to have several information going into a single `BOOST_TEST_CONTEXT`, to avoid precisely having several scopes for a single context.

---

## Comment 4

> Username: dudamoos  
> Created at: 2019-02-08 16:48:33 UTC  
> Url: https://github.com/boostorg/test/issues/197#issuecomment-461867743  

Yes. I need the context to span several assertions. I understand why you made `BOOST_TEST_CONTEXT` create a scope that the context will apply to. However, in my case I want the context to last for the rest of the enclosing scope. Is your last statement supposed to mean that adding context to the enclosing scope is an explicit non-goal?

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2019-02-08 17:00:09 UTC  
> Url: https://github.com/boostorg/test/issues/197#issuecomment-461871756  

It is not an explicit non-goal, but I need to think about it.

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2019-02-11 00:06:06 UTC  
> Updated at: 2019-02-11 00:06:36 UTC  
> Url: https://github.com/boostorg/test/issues/197#issuecomment-462194519  

Ok, I implemented this via a new macro `BOOST_TEST_INFO_SCOPE`. Example of use  
  
```  
  for(int i = 0; i < 50; i++) {  
    BOOST_TEST_INFO_SCOPE("trial " << i+1);  
    int root1 = dis(gen);  
    int root2 = dis(gen);  
    if(root1 > root2) {  
      std::swap(root1, root2);  
    }  
    BOOST_TEST_INFO_SCOPE("root1 = " << root1);  
    BOOST_TEST_INFO_SCOPE("root2 = " << root2);  
  
    std::pair<double, double> estimated = estimate_polynomial_roots(  
      gen,  
      [root1, root2](double x) -> double { return (x - root1) * (x - root2); });  
  
    BOOST_TEST(estimated.first == double(root1), 10. % boost::test_tools::tolerance());  
    BOOST_TEST(estimated.second == double(root2), 10. % boost::test_tools::tolerance());  
  }  
```  
  
Let me know if that corresponds to what you asked (same branch as before if you want to give a try).

---

## Comment 7

> Username: dudamoos  
> Created at: 2019-02-13 01:11:29 UTC  
> Url: https://github.com/boostorg/test/issues/197#issuecomment-463012350  

That fix does the trick. I'll work on getting everyone here using an upgraded or patched version. Thanks!
