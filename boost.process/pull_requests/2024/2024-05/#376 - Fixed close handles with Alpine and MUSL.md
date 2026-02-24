# #376 Fixed close handles  with Alpine and MUSL [Closed]

> Username: dchansen  
> Created at: 2024-05-30 06:59:54 UTC  
> Updated at: 2024-12-20 02:01:26 UTC  
> Closed at: 2024-12-20 02:01:25 UTC  
> Url: https://github.com/boostorg/process/pull/376  

The MUSL C library does not provide the close_range in unistd.h and Alpine linux therefore cannot compile boost::process::v2, at close_handles.ipp fails to compile.  
  
This PR adds a check to only use close_range if we are using GLIBC.

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2024-05-30 07:23:49 UTC  
> Url: https://github.com/boostorg/process/pull/376#issuecomment-2138852297  

Looks good, will merge if all tests pass.

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2024-06-04 00:01:19 UTC  
> Url: https://github.com/boostorg/process/pull/376#issuecomment-2146322987  

Ok, I don't think this is correct, as it doesn't handle libc++. I'll need to add a MUSL test to CI.

---

## Comment 3

> Username: T3RR7  
> Created_at: 2024-06-18 18:19:46 UTC  
> Url: https://github.com/boostorg/process/pull/376#issuecomment-2176701705  

Hello, @dchansen, I've encountered the same issue, but with glibc. The `close_range` checks here would be a bit more complicated. I can include your changes in my [patch](https://github.com/boostorg/process/pull/378), so it could resolve 2 issues with single PR.

---

## Comment 4

> Username: dchansen  
> Created_at: 2024-06-20 06:48:22 UTC  
> Url: https://github.com/boostorg/process/pull/376#issuecomment-2179941643  

@t43rr7 That looks good to me. Could you make a PR for my branch? then I will merge it in.

---

## Comment 5

> Username: T3RR7  
> Created_at: 2024-06-20 10:56:39 UTC  
> Updated_at: 2024-06-20 11:02:28 UTC  
> Url: https://github.com/boostorg/process/pull/376#issuecomment-2180391276  

@dchansen I've opened PR to your branch. Looks like your branch's HEAD a bit behind origin develop, so there're a bit more changes that expected. Anyway, the most interesting diffs [here](https://github.com/dchansen/process/pull/1/files#diff-d2237ff6720cb6f4cd709d6300e6242b33f036ccb3b19bbb54768e99e1a976bbR14). I've also added raw system call support which should work even on MUSL library.   
  
Please, verify this changes on your setup, because I have no possibility to do it fast (with MUSL). Thanks!  
  
UPD:   
Same changes can be verified [in this PR](https://github.com/boostorg/process/pull/378/files)

---

## Comment 6

> Username: T3RR7  
> Created_at: 2024-06-21 14:26:42 UTC  
> Url: https://github.com/boostorg/process/pull/376#issuecomment-2182860937  

@klemens-morgenstern please, look at the new changes

---

## Comment 7

> Username: klemens-morgenstern  
> Created_at: 2024-06-21 14:37:05 UTC  
> Url: https://github.com/boostorg/process/pull/376#issuecomment-2182879286  

Can't we just put a `close_range` wrapper around the syscall into a detail namespace and use `using ::close_range` otherwise? That way we don't need to reimpl `close_all`.

---

## Comment 8

> Username: T3RR7  
> Created_at: 2024-06-21 14:47:47 UTC  
> Updated_at: 2024-06-21 14:51:41 UTC  
> Url: https://github.com/boostorg/process/pull/376#issuecomment-2182897970  

Do you mean something like "code below"?  
  
```cpp  
  
int close_range_wrapper(...) {  
#ifdef HAS_CLOSE_RANGE  
    return ::close_range(...);  
#elif defined(HAS_CLOSE_RANGE_SYSCALL)   
    return ::syscall(SYS_close_range, ...);  
#endif  
}  
```  
  
I like this idea :)  
But this PR is not from my fork, I can do it in s[imilar opened PR](https://github.com/boostorg/process/pull/378). How do you feel about this?

---

## Comment 9

> Username: T3RR7  
> Created_at: 2024-06-21 15:23:27 UTC  
> Url: https://github.com/boostorg/process/pull/376#issuecomment-2182961531  

@klemens-morgenstern done in [this commit](https://github.com/boostorg/process/pull/378/commits/40350de7ebeb353178b9fa6b8bb9d0f0ef0b6efc)  
  
Please, see [PR](https://github.com/boostorg/process/pull/378)

---

## Comment 10

> Username: T3RR7  
> Created_at: 2024-06-25 18:19:13 UTC  
> Url: https://github.com/boostorg/process/pull/376#issuecomment-2189669305  

@klemens-morgenstern are there any news?

---

## Comment 11

> Username: klemens-morgenstern  
> Created_at: 2024-12-20 02:01:25 UTC  
> Url: https://github.com/boostorg/process/pull/376#issuecomment-2556112549  

I think this is fixed in develop already..

---
