# #176 - [snippet_dataset1_3] seems to be broken [Closed]

> Username: green-anger  
> Created at: 2018-11-06 17:42:53 UTC  
> Updated at: 2019-02-18 21:21:15 UTC  
> Closed at: 2019-02-18 21:20:51 UTC  
> Url: https://github.com/boostorg/test/issues/176  

There's a link to [snippet_dataset1_3]:  
https://github.com/boostorg/test/blob/2b1e4cdda0c8526d9d2967d7e6fbe4818600283a/doc/test_organization/parametric_test_case_generation.qbk#L193-L196  
  
Here is the snippet:  
https://github.com/boostorg/test/blob/2b1e4cdda0c8526d9d2967d7e6fbe4818600283a/doc/snippet/dataset_1/test_file.cpp#L38-L44  
  
But according to this  
https://github.com/boostorg/test/blob/2b1e4cdda0c8526d9d2967d7e6fbe4818600283a/doc/test_organization/parametric_test_case_generation.qbk#L306-L310  
  
the snippet must throw. And it does throw (msvc 19.15.26732.1 for x86 + boost.test 1.67.0).  
  
So is it a broken snippet? I didn't find any mentions in the docs this to be intentional behavior.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-11-06 18:49:05 UTC  
> Url: https://github.com/boostorg/test/issues/176#issuecomment-436364874  

Thanks you for raising this, and I agree that this is not the best way to communicate over Boost.Test features.   
  
I'll change the snippet for the next version.

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2019-02-10 19:40:34 UTC  
> Url: https://github.com/boostorg/test/issues/176#issuecomment-462164726  

Would this be ok? 1cd5487b32bf5fc0427aca5c4077f7d9525d6335
