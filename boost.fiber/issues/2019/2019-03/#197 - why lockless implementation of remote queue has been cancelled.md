# #197 - why lockless implementation of remote queue has been cancelled? [Closed]

> Username: romange  
> Created at: 2019-03-12 16:35:47 UTC  
> Updated at: 2020-01-13 07:45:41 UTC  
> Closed at: 2020-01-13 07:45:41 UTC  
> Url: https://github.com/boostorg/fiber/issues/197  

Hi Oliver,   
  
do you remember why you erased Vyukov's lockless queue for remote-ready use-case?  
https://github.com/boostorg/fiber/commit/0d62d7c5d25a203c1a261a2884067b689c364c4c  
  
Incidentally, I've added it to my version of fibers lib and only then discovered you've already tried it...

---

## Comment 1

> Username: olk  
> Created at: 2020-01-13 07:45:41 UTC  
> Url: https://github.com/boostorg/fiber/issues/197#issuecomment-573542852  

I can't remember - sorry (probably because of performance issues)
