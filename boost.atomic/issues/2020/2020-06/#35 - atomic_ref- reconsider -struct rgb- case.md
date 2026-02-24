# #35 - atomic_ref: reconsider "struct rgb" case [Closed]

> Username: AlexGuteniev  
> Created at: 2020-06-07 13:54:33 UTC  
> Updated at: 2020-06-07 15:32:06 UTC  
> Closed at: 2020-06-07 14:23:51 UTC  
> Url: https://github.com/boostorg/atomic/issues/35  

Regarding `atomic_ref` with  
```C++  
struct rgb  
{  
    unsigned char r, g, b; // 3 bytes  
};  
```  
  
It can be implemented by implementing every operation (including load) via CAS loop of wider type.  
But may be still not worth doing this way.  
  
See this thread: https://github.com/ORNL/cpp-proposals-pub/issues/130

---

## Comment 1

> Username: Lastique  
> Created at: 2020-06-07 14:23:51 UTC  
> Url: https://github.com/boostorg/atomic/issues/35#issuecomment-640226582  

It cannot be implemented because the padding bits are undefined and there is no portable way to clear them. Types with padding bits are unsupported by Boost.Atomic with a sole exception of `long double` on x86.

---

## Comment 2

> Username: AlexGuteniev  
> Created at: 2020-06-07 14:26:58 UTC  
> Url: https://github.com/boostorg/atomic/issues/35#issuecomment-640226990  

I think you confused two recently created issues. This one is about odd sized types, not padding bits.  
  
Also regarding padding bits, I understand it cannot be implemented, I just think that caveats section in the documentation should be expanded.

---

## Comment 3

> Username: Lastique  
> Created at: 2020-06-07 14:41:33 UTC  
> Url: https://github.com/boostorg/atomic/issues/35#issuecomment-640229157  

It doesn't matter how padding bits are introduced - either for member alignment to a byte boundary (which is the case with bit fields) or to its natural alignment (which may be the case when the padding is inserted between members) or to align the structure size to a multiple of its own alignment (which is the case with the trailing padding).  
  
In the particular example with `struct rgb` and `atomic_ref`, what you suggest (ignoring the alignment issue for the sake of discussion) would effectively consider the byte immediately after `struct rgb` object as a padding byte (assuming there are 32-bit atomic operations). Given that its contents are undefined, and that we're not actually allowed to modify it, makes the operation not feasible.

---

## Comment 4

> Username: AlexGuteniev  
> Created at: 2020-06-07 14:48:07 UTC  
> Url: https://github.com/boostorg/atomic/issues/35#issuecomment-640230141  

With compare exchange, not modifying content can be accomplished by compare-exchange, and in case of failure due to bits not belonging to value, retry with changing the expectation for these bits   
  
See also this comment: https://github.com/ORNL/cpp-proposals-pub/issues/130#issuecomment-634194203  
  
Alignment is harder, but can be dealt with too. First, on some platforms, only plain loads/stores are subject to tearing due to misalignment, CAS would work. Second, you need to prevent accessing memory page that not belongs to the value to avoid access violation. But is is all doable. And third, `required_aligmnent` is not prohibited to be bigger than `sizeof`, though it defeats the purpose of `atomic_ref` so should not be done.

---

## Comment 5

> Username: Lastique  
> Created at: 2020-06-07 15:13:38 UTC  
> Url: https://github.com/boostorg/atomic/issues/35#issuecomment-640233800  

> With compare exchange, not modifying content can be accomplished by compare-exchange, and in case of failure due to bits not belonging to value, retry with changing the expectation for these bits  
  
I'm not sure that would work portably. For instance, on x86, IIRC, atomic instructions only synchronize if they are applied on the same address and the memory operands have the same size. So, for example, if you have this kind of code:  
  
```  
rgb a;  
unsigned char b;  
  
atomic_ref< rgb > ra(a);  
atomic_ref< unsigned char > rb(b);  
```  
  
I don't think operations through `ra` would synchronize with operations through `rb`, meaning that updates to `a` or `b` might not be atomic from architectural POV. I'm not sure how other architectures behave in that regard, but in any case, I don't want to allow potential side effects on unrelated user's data through `atomic_ref`. If you want lock-free operations on `rgb` then use `atomic<rgb>`.  
  
> First, on some platforms, only plain loads/stores are subject to tearing due to misalignment, CAS would work.  
  
To my knowledge, this is only kind of allowed on x86 (for *some* atomic sizes), and it is gradually becoming illegal even there. See [split-lock detection](https://lwn.net/Articles/790464/) for instance.

---

## Comment 6

> Username: AlexGuteniev  
> Created at: 2020-06-07 15:32:06 UTC  
> Url: https://github.com/boostorg/atomic/issues/35#issuecomment-640236311  

Ok, understood, if you are looking into portable solutions, then yes, agreed, `atomic_ref<rgb>` cannot be lock-free.
