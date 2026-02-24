# #87 handle xml writing of null keys [Closed]

> Username: brchrisman  
> Created at: 2021-06-08 01:00:04 UTC  
> Updated at: 2021-06-15 17:52:11 UTC  
> Closed at: 2021-06-15 17:52:11 UTC  
> Url: https://github.com/boostorg/property_tree/pull/87  

boost::property_tree can have nulls as keys.  
In particular, json read typically generates child nodes with null keys  
as part of array processing.  
Place a nonce (3cb6534e-d358-4705-9e74-fee06453661e) as a unique tag  
name for null keys in write_xml.

---

## Comment 1

> Username: madmongo1  
> Created_at: 2021-06-08 09:04:01 UTC  
> Url: https://github.com/boostorg/property_tree/pull/87#issuecomment-856595535  

Thank you for the contribution.  
  
Is there any way we can add a test to:  
- demonstrate no legacy impact  
- test the problem that this fixes  
- demonstrate that it works in C++03

---

## Comment 2

> Username: madmongo1  
> Created_at: 2021-06-08 09:04:45 UTC  
> Url: https://github.com/boostorg/property_tree/pull/87#issuecomment-856596035  

I currently see test failures in C++03:  
  
```  
gcc.compile.c++ bin.v2/libs/property_tree/test/test_multi_module1.test/gcc-9/debug/cxxstd-03-iso/link-static/threading-multi/visibility-hidden/test_multi_module2.o  
In file included from ./boost/property_tree/xml_parser.hpp:15,  
                 from libs/property_tree/test/test_multi_module2.cpp:14:  
./boost/property_tree/detail/xml_parser_write.hpp: In function ‘void boost::property_tree::xml_parser::write_xml_element(std::basic_ostream<typename Ptree::key_type::value_type>&, const typename Ptree::key_type&, const Ptree&, int, const boost::property_tree::xml_parser::xml_writer_settings<typename Ptree::key_type>&)’:  
./boost/property_tree/detail/xml_parser_write.hpp:75:31: error: ‘R’ was not declared in this scope  
   75 |         const char* nullkey = R"nullkey(nullkey-3cb6534e-d358-4705-9e74-fee06453661e)nullkey";  
      |                               ^  
```

---

## Comment 3

> Username: brchrisman  
> Created_at: 2021-06-08 15:47:52 UTC  
> Url: https://github.com/boostorg/property_tree/pull/87#issuecomment-856886435  

> Thank you for the contribution.  
>   
> Is there any way we can add a test to:  
>   
> * demonstrate no legacy impact  
> * test the problem that this fixes  
> * demonstrate that it works in C++03  
  
Thanks for the feedback, I will look through the tests and move the string literal to c++03 compatibility.

---

## Comment 4

> Username: brchrisman  
> Created_at: 2021-06-09 01:17:07 UTC  
> Url: https://github.com/boostorg/property_tree/pull/87#issuecomment-857297877  

Fixed c++03 compatibility.  Added ptree key-nulling on xml ingestion for the same nullkey-<uuid> nonce and included a test with the 'total key size' showing that the nonce is not included in the keys.  
I don't have the exact build tree, but checked those values on an environment with this change integrated.

---

## Comment 5

> Username: brchrisman  
> Created_at: 2021-06-10 16:08:41 UTC  
> Url: https://github.com/boostorg/property_tree/pull/87#issuecomment-858752801  

I'm not sure how to trigger another run of automated build/testing or whether I can, but it should be ready.  
thanks,  
Brian

---

## Comment 6

> Username: brchrisman  
> Created_at: 2021-06-15 17:50:39 UTC  
> Url: https://github.com/boostorg/property_tree/pull/87#issuecomment-861709773  

Pushed fixes here and was wondering whether the automated testing can be  
run to verify.  
i'm not sure how to signal that the PR has been changed and/or is ready.  
  
On Tue, Jun 8, 2021 at 2:05 AM Richard Hodges ***@***.***>  
wrote:  
  
> I currently see test failures in C++03:  
>  
> gcc.compile.c++ bin.v2/libs/property_tree/test/test_multi_module1.test/gcc-9/debug/cxxstd-03-iso/link-static/threading-multi/visibility-hidden/test_multi_module2.o  
>  
> In file included from ./boost/property_tree/xml_parser.hpp:15,  
>  
>                  from libs/property_tree/test/test_multi_module2.cpp:14:  
>  
> ./boost/property_tree/detail/xml_parser_write.hpp: In function ‘void boost::property_tree::xml_parser::write_xml_element(std::basic_ostream<typename Ptree::key_type::value_type>&, const typename Ptree::key_type&, const Ptree&, int, const boost::property_tree::xml_parser::xml_writer_settings<typename Ptree::key_type>&)’:  
>  
> ./boost/property_tree/detail/xml_parser_write.hpp:75:31: error: ‘R’ was not declared in this scope  
>  
>    75 |         const char* nullkey = R"nullkey(nullkey-3cb6534e-d358-4705-9e74-fee06453661e)nullkey";  
>  
>       |                               ^  
>  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/property_tree/pull/87#issuecomment-856596035>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AAFVWM2XRT4RVMCJDKCSOADTRXML3ANCNFSM46IZCHYA>  
> .  
>

---
