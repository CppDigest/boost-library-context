# #304 - Is boost test thread safe? [Closed]

> Username: jonesmz  
> Created at: 2021-03-18 19:47:37 UTC  
> Updated at: 2022-03-08 17:50:25 UTC  
> Closed at: 2022-03-08 17:50:24 UTC  
> Url: https://github.com/boostorg/test/issues/304  

I see no documentation about threads anywhere in https://www.boost.org/doc/libs/1_75_0/libs/test/doc/html/index.html  
  
I am also failing to find references to thread safety by searching the code here in github.  
  
Should I expect this code to work?  
  
```cpp  
BOOST_TEXT_CASE(test_parallel)  
{  
    std::vector<std::string> vect(1024u);  
    std::for_each(  
        std::execution::par_unseq,  
        vect.begin(),  
        vect.end(),  
        [](auto const& str)  
        {  
            BOOST_CHECK_EQUAL(str, "");  
        });  
}  
```  
?

---

## Comment 1

> Username: jonesmz  
> Created at: 2021-03-29 18:47:28 UTC  
> Url: https://github.com/boostorg/test/issues/304#issuecomment-809623572  

Hi. This is hopefully a simple question to answer.  
  
Is BOOST_TEST thread safe or not?

---

## Comment 2

> Username: jonesmz  
> Created at: 2021-06-10 17:32:35 UTC  
> Url: https://github.com/boostorg/test/issues/304#issuecomment-858818911  

This is a very simple question.  
  
Can no project member answer it?

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2021-06-10 17:39:03 UTC  
> Url: https://github.com/boostorg/test/issues/304#issuecomment-858827339  

Boost.Test is currently not thread safe.

---

## Comment 4

> Username: jonesmz  
> Created at: 2021-06-10 17:51:08 UTC  
> Url: https://github.com/boostorg/test/issues/304#issuecomment-858838130  

Can that be clearly documented in the manual then?  
  
https://www.boost.org/doc/libs/1_76_0/libs/test/doc/html/boost_test/section_faq.html <-

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2022-03-05 22:06:46 UTC  
> Url: https://github.com/boostorg/test/issues/304#issuecomment-1059838632  

In branch `topic/doc-update`

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2022-03-08 17:50:24 UTC  
> Url: https://github.com/boostorg/test/issues/304#issuecomment-1062044070  

Merged in https://github.com/boostorg/test/pull/337 rev https://github.com/boostorg/test/commit/b52b0ea6429c393485f8ea27ab5edd678823e65a.
