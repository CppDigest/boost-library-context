# #158 - Compile error when using MP11 with GMP multiprecision [Closed]

> Username: dancooke  
> Created at: 2019-09-10 14:18:08 UTC  
> Updated at: 2019-09-10 17:18:48 UTC  
> Closed at: 2019-09-10 17:18:47 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/158  

See [this issue](https://github.com/boostorg/log/issues/90), in particular the [reduced test case](https://github.com/boostorg/log/issues/90#issuecomment-529955252).

---

## Comment 1

> Username: dancooke  
> Created at: 2019-09-10 14:18:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/158#issuecomment-529957266  

Isse also [reported](https://github.com/boostorg/mp11/issues/41) on MP11.

---

## Comment 2

> Username: dancooke  
> Created at: 2019-09-10 17:18:47 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/158#issuecomment-530036163  

Turns out to be a [Clang bug](https://bugs.llvm.org/show_bug.cgi?id=43266).
