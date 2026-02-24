# #28 - Which branch to use currently? [Closed]

> Username: erakadjiev  
> Created at: 2015-02-05 01:51:15 UTC  
> Updated at: 2015-02-05 10:13:28 UTC  
> Closed at: 2015-02-05 10:13:28 UTC  
> Url: https://github.com/boostorg/fiber/issues/28  

Hello Oliver,  
  
A project I'm working on will rely on Boost Fiber and I have to start using the library in the next few days.   
Which branch is the one you recommend to use currently? The `master` is very old, so I was looking at `develop`, but there are some newer ones, too.  
  
Thank you for the great libraries!

---

## Comment 1

> Username: olk  
> Created at: 2015-02-05 06:56:42 UTC  
> Url: https://github.com/boostorg/fiber/issues/28#issuecomment-73002746  

Please note that boost.fiber is still in development -at the moment I'm evaluating different implementations. I preferre branch ctx_onstack_lambda at the moment.

---

## Comment 2

> Username: erakadjiev  
> Created at: 2015-02-05 10:13:28 UTC  
> Url: https://github.com/boostorg/fiber/issues/28#issuecomment-73023131  

Thank you for the quick answer! Yes, I'm aware of the fact that the library is still in development. The project in which we have to use it is not a production project yet, so as long as a version of Boost Fiber "mostly works", it should be good for us. I'll go ahead with `ctx_onstack_lambda`, thanks!
