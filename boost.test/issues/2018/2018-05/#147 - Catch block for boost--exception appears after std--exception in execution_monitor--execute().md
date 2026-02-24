# #147 - Catch block for boost::exception appears after std::exception in execution_monitor::execute() [Closed]

> Username: k15tfu  
> Created at: 2018-05-23 15:41:41 UTC  
> Updated at: 2019-03-14 11:29:03 UTC  
> Closed at: 2018-10-02 00:04:50 UTC  
> Url: https://github.com/boostorg/test/issues/147  

So, if I throw myexception defined as `struct myexception : std::exception, virtual boost::exception` it prints uninformative message

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-05-23 16:01:54 UTC  
> Url: https://github.com/boostorg/test/issues/147#issuecomment-391402319  

Give a snippet, define uninformative, and please read the contribution file on how to file a bug ticket.

---

## Comment 2

> Username: k15tfu  
> Created at: 2018-05-24 12:53:29 UTC  
> Updated at: 2018-05-24 12:55:14 UTC  
> Url: https://github.com/boostorg/test/issues/147#issuecomment-391703522  

If you prefer to use Boost Trac why you haven't disabled Issues tab on GitHub?  
  
```  
using error_code = boost::error_info<struct tag_error_code, uint32_t>;  
struct myexception : std::exception, virtual boost::exception {};  
  
BOOST_AUTO_TEST_CASE(is_always_lock_free) {  
  BOOST_THROW_EXCEPTION(myexception << error_code(123));  
}  
```  
  
At the moment the exception will be catch as `std::exception`:  
```  
test.cpp(66): fatal error: in "void __cdecl `anonymous-namespace'::is_always_lock_free::test_method(void)": class boost::exception_detail::clone_impl<struct myexception>: Unknown exception  
```  
  
Well.. I swapped the boost::exception && std::exception and the only difference was in showing "Unknown exception" message.  Sorry, thought it will use boost::diagnostic_information() instead of unknown-exception phrase.  
  
But isn't it right to catch more narrowed (non generic) exceptions first?

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2018-09-02 18:33:34 UTC  
> Updated at: 2018-09-02 18:33:42 UTC  
> Url: https://github.com/boostorg/test/issues/147#issuecomment-417950185  

What happens if you derive only from `boost::exception`? and recently Trac has been closed to new tickets.

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2018-09-12 21:21:26 UTC  
> Url: https://github.com/boostorg/test/issues/147#issuecomment-420802164  

@k15tfu Would you please test the branch `topic/GH-147-boost-exception-before-std-exception`?

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2018-09-29 19:23:44 UTC  
> Url: https://github.com/boostorg/test/issues/147#issuecomment-425669703  

In next

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2018-10-02 00:04:50 UTC  
> Url: https://github.com/boostorg/test/issues/147#issuecomment-426103131  

In develop, closing.

---

## Comment 7

> Username: k15tfu  
> Created at: 2019-03-14 11:29:03 UTC  
> Url: https://github.com/boostorg/test/issues/147#issuecomment-472812464  

Tested, thanks!
