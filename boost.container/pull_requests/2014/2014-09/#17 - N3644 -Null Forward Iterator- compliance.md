# #17 N3644 "Null Forward Iterator" compliance [Closed]

> Username: mikael-s-persson  
> Created at: 2014-09-24 05:42:28 UTC  
> Updated at: 2014-09-24 14:12:49 UTC  
> Closed at: 2014-09-24 14:12:49 UTC  
> Url: https://github.com/boostorg/container/pull/17  

Updated vector and stable-vector iterators for n3644 compliance.  
Added n3644 compliance tests to all container iterators.  
  
Boost.Intrusive commit needed too (maybe more are needed there too, if all intrusive containers are going to comply to n3644 too).

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2014-09-24 08:33:58 UTC  
> Url: https://github.com/boostorg/container/pull/17#issuecomment-56640181  

I've nearly finished a patch to implement n3644, which is similar to your patch, plus some documentation issues. I'm testing it in several compilers. I'll review your patch in case I'm missing something. I didn't know you were working on this, I was about to reply your e-mail today.

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2014-09-24 14:12:49 UTC  
> Url: https://github.com/boostorg/container/pull/17#issuecomment-56675315  

Already commited in:  
  
SHA-1: 3af96e0801773e6cb917f58541093806a1a9fa27  
- Implements N3644.  
- Avoid deriving from std::iterator_traits as iterators become too fat.  
- Use intrusive's reverse_iterator some std::reverse_iterator-s don't value initialize base.  
  
Thanks

---
