# #320 Fixed optimized away hooks. Fixes #316 [Merged]

> Username: Kojoley  
> Created at: 2020-06-14 14:15:43 UTC  
> Updated at: 2020-07-04 18:23:23 UTC  
> Merged at: 2020-07-03 12:55:56 UTC  
> Closed at: 2020-07-03 12:55:56 UTC  
> Url: https://github.com/boostorg/thread/pull/320  

MSVC learned to not emit unreferenced symbols with internal linkage and the  
hooks were defined in unnamed namespace which forces internal linkage, even if  
you mark a variable `extern`.  
  
Since Boost does not have a stable ABI, does not mangle the namespace with  
the version, and the hooks are in `boost` namespace (`boost::on_*`) -- there is  
no point in trying to hide some symbols because mixing different versions of  
boost static libraries will not work already.  
  
I also renamed the `__xl_ca` variable for consistency and because using double  
underscored identifiers is forbidden. (`[lex.name]/3`)  
  
The `extern const` is for verbosity and because they are indeed const (it is  
done via pragma already).

---

## Comment 1

> Username: Kojoley  
> Created_at: 2020-06-14 14:28:39 UTC  
> Url: https://github.com/boostorg/thread/pull/320#issuecomment-643774138  

I did not add a test because `tss_test` is already catching the issue. I also checked that this fixes Clang build which had the issue a long before.

---

## Comment 2

> Username: Lastique  
> Created_at: 2020-07-02 23:47:26 UTC  
> Url: https://github.com/boostorg/thread/pull/320#issuecomment-653263344  

@pdimov Since @viboes is not responding, maybe you could merge this fix?

---

## Comment 3

> Username: pdimov  
> Created_at: 2020-07-03 12:57:10 UTC  
> Url: https://github.com/boostorg/thread/pull/320#issuecomment-653535760  

OK, done. I'm getting crashes in some `future` tests on `clang-win`, but that's a preexisting problem. Maybe @Kojoley knows why.

---

## Comment 4

> Username: Lastique  
> Created_at: 2020-07-03 14:40:58 UTC  
> Url: https://github.com/boostorg/thread/pull/320#issuecomment-653578888  

Thanks, Peter. Please, remember to merge to master as well.

---

## Comment 5

> Username: pdimov  
> Created_at: 2020-07-03 18:23:49 UTC  
> Url: https://github.com/boostorg/thread/pull/320#issuecomment-653642850  

I will.  
  
Looking into the clang-win issue, one potential problem might be that at https://github.com/boostorg/thread/blob/96cd717b331e62095aa58d6768dab3baa03fcdce/src/win32/tss_pe.cpp#L219-L227 Clang issues a warning that `dw` is unused, so it's probably optimized out and the reference to `_tls_used` is eliminated.

---

## Comment 6

> Username: Kojoley  
> Created_at: 2020-07-03 18:32:33 UTC  
> Url: https://github.com/boostorg/thread/pull/320#issuecomment-653644629  

> Maybe @Kojoley knows why.  
  
I have no idea. It crashes with access violation on trying to rethrow/read the exception from the future, reproducible only on 32bit build with optimizations.  
  
> Clang issues a warning that dw is unused, so it's probably optimized out and the reference to _tls_used is eliminated.  
  
When I was debugging the #316 issue I saw the symbol, it is not optimized away, and since it is marked as C extern I am not sure the comment makes any sense.

---

## Comment 7

> Username: Lastique  
> Created_at: 2020-07-03 18:33:04 UTC  
> Url: https://github.com/boostorg/thread/pull/320#issuecomment-653644730  

If it is indeed optimized out, I would call it a compiler bug. The warning does not indicate that it is removed, though.

---

## Comment 8

> Username: pdimov  
> Created_at: 2020-07-03 21:08:42 UTC  
> Url: https://github.com/boostorg/thread/pull/320#issuecomment-653677766  

This looks like a clang codegen bug. In  
```  
	boost::promise<int> p;  
	p.set_exception( boost::copy_exception( X() ) );  
```  
`boost::copy_exception` returns a valid `exception_ptr`, and `set_exception` receives an invalid one. Oh well.

---

## Comment 9

> Username: Kojoley  
> Created_at: 2020-07-03 22:24:46 UTC  
> Url: https://github.com/boostorg/thread/pull/320#issuecomment-653688625  

> This looks like a clang codegen bug  
  
I was guessing it to be also, simply because Clang has known issues with exceptions on MS ABI (https://groups.google.com/forum/#!topic/llvm-dev/ZcNUP_1550M).

---

## Comment 10

> Username: Kojoley  
> Created_at: 2020-07-03 22:48:43 UTC  
> Url: https://github.com/boostorg/thread/pull/320#issuecomment-653691139  

Weird thing, this crashes:  
```cpp  
#include <boost/thread/future.hpp>  
#include <thread>  
  
struct wrap  
{  
};  
  
template <typename T>  
boost::exception_ptr make_exception_ptr(T) {  
  return boost::copy_exception(wrap{});  
}  
  
void func2(boost::promise<int> p)  
{  
    p.set_exception(make_exception_ptr(3));  
}  
  
  
int main()  
{  
    boost::promise<int> p;  
    auto f = p.get_future();  
    std::thread(func2, std::move(p)).detach();  
    try  
    {  
        f.get();  
    }  
    catch (wrap const&)  
    {  
        return 0;  
    }  
    return 1;  
}  
```  
And this does not:  
```cpp  
#include <boost/thread/future.hpp>  
#include <thread>  
  
struct wrap  
{  
};  
  
boost::exception_ptr make_exception_ptr(int) {  
  return boost::copy_exception(wrap{});  
}  
  
void func2(boost::promise<int> p)  
{  
    p.set_exception(make_exception_ptr(3));  
}  
  
  
int main()  
{  
    boost::promise<int> p;  
    auto f = p.get_future();  
    std::thread(func2, std::move(p)).detach();  
    try  
    {  
        f.get();  
    }  
    catch (wrap const&)  
    {  
        return 0;  
    }  
    return 1;  
}  
```  
  
```diff  
-template <typename T>  
-boost::exception_ptr make_exception_ptr(T) {  
+boost::exception_ptr make_exception_ptr(int) {  
   return boost::copy_exception(wrap{});  
 }  
```

---

## Comment 11

> Username: pdimov  
> Created_at: 2020-07-03 23:10:28 UTC  
> Url: https://github.com/boostorg/thread/pull/320#issuecomment-653693275  

Probably affects what's getting inlined where. This is what crashes for me:  
```  
#include <boost/thread/future.hpp>  
  
struct X  
{  
};  
  
int main()  
{  
    boost::promise<int> p;  
    boost::unique_future<int> f = p.get_future();  
  
    p.set_exception( boost::copy_exception( X() ) );  
  
    try  
    {  
        f.get();  
    }  
    catch( X const& )  
    {  
    }  
}  
```  
No need to use a thread at all. The `p.set_exception` line is miscompiled. I suppose one doesn't even need `future` here.

---

## Comment 12

> Username: pdimov  
> Created_at: 2020-07-03 23:17:53 UTC  
> Url: https://github.com/boostorg/thread/pull/320#issuecomment-653694039  

Yeah, this crashes too:  
```  
#include <boost/exception_ptr.hpp>  
  
struct X  
{  
};  
  
void set_exception( boost::exception_ptr p )  
{  
    try  
    {  
        boost::rethrow_exception( p );  
    }  
    catch( X const& x )  
    {  
    }  
}  
  
int main()  
{  
    set_exception( boost::copy_exception( X{} ) );  
}  
```

---

## Comment 13

> Username: Lastique  
> Created_at: 2020-07-03 23:40:29 UTC  
> Url: https://github.com/boostorg/thread/pull/320#issuecomment-653696275  

If it's caused by inlining, does `BOOST_NOINLINE` applied to `set_exception` help?

---

## Comment 14

> Username: pdimov  
> Created_at: 2020-07-03 23:45:54 UTC  
> Url: https://github.com/boostorg/thread/pull/320#issuecomment-653696734  

In that last example, changing `set_exception` to take by `const&` eliminates the crash.  
  
But I'm not sure how much we need to care about this issue, as it only affects clang-cl 32 bit and nobody has reported it as a problem.

---

## Comment 15

> Username: Lastique  
> Created_at: 2020-07-03 23:50:38 UTC  
> Url: https://github.com/boostorg/thread/pull/320#issuecomment-653697161  

I'd say, we shouldn't wait until it hits someone, especially if we have a workaround.

---

## Comment 16

> Username: pdimov  
> Created_at: 2020-07-04 16:28:20 UTC  
> Url: https://github.com/boostorg/thread/pull/320#issuecomment-653785921  

The right thing to do is to file a bug against Clang, then maybe work around it.

---

## Comment 17

> Username: Kojoley  
> Created_at: 2020-07-04 18:23:23 UTC  
> Url: https://github.com/boostorg/thread/pull/320#issuecomment-653797011  

https://bugs.llvm.org/show_bug.cgi?id=46584

---
