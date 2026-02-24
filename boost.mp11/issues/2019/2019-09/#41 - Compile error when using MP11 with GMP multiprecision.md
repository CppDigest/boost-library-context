# #41 - Compile error when using MP11 with GMP multiprecision [Closed]

> Username: dancooke  
> Created at: 2019-09-10 14:18:05 UTC  
> Updated at: 2019-09-10 17:18:23 UTC  
> Closed at: 2019-09-10 17:18:23 UTC  
> Url: https://github.com/boostorg/mp11/issues/41  

See [this issue](https://github.com/boostorg/log/issues/90), in particular the [reduced test case](https://github.com/boostorg/log/issues/90#issuecomment-529955252).

---

## Comment 1

> Username: dancooke  
> Created at: 2019-09-10 14:18:57 UTC  
> Url: https://github.com/boostorg/mp11/issues/41#issuecomment-529957468  

Issue also [reported](https://github.com/boostorg/multiprecision/issues/158) on multiprecision.

---

## Comment 2

> Username: pdimov  
> Created at: 2019-09-10 16:10:24 UTC  
> Url: https://github.com/boostorg/mp11/issues/41#issuecomment-530009374  

Looks like a Clang parser bug:  
  
```  
template<class L> struct mp_size;  
  
struct __mpf_struct  
{  
    int _mp_size;  
};  
  
struct X  
{  
    __mpf_struct m_data[1];  
      
    void f()  
    {  
        if(((m_data)->_mp_size < 0 ? -1: (m_data)->_mp_size > 0) == 0)  
        {  
        }  
    }  
};  
```  
  
Not sure what we can do about it.

---

## Comment 3

> Username: dancooke  
> Created at: 2019-09-10 17:18:15 UTC  
> Url: https://github.com/boostorg/mp11/issues/41#issuecomment-530035969  

Thanks. [Reported](https://bugs.llvm.org/show_bug.cgi?id=43266).
