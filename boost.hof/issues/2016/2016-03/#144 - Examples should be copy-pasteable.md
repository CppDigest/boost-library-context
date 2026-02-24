# #144 - [Doc] Examples should be copy-pasteable [Closed]

> Username: ldionne  
> Created at: 2016-03-11 21:33:34 UTC  
> Updated at: 2016-03-26 02:10:22 UTC  
> Closed at: 2016-03-26 02:10:22 UTC  
> Url: https://github.com/boostorg/hof/issues/144  

I find it very useful to be able to copy/paste examples from the documentation and have them work as-is. Having to tinker with an example like adding includes, adding `using namespace fit` or similar things is annoying and counter productive.

---

## Comment 1

> Username: pfultz2  
> Created at: 2016-03-11 22:29:38 UTC  
> Url: https://github.com/boostorg/hof/issues/144#issuecomment-195583206  

Well there is an 'example.h'(which I realize should be 'example.hpp') header that can be included to take of that. However, even with the headers, not all of the examples are copy and pasteable. This is because parts of the example should go in the main body and other parts should go into the global scope. Right now, when I test the examples, I use a bunch of crazy logic([here](https://github.com/pfultz2/Fit/blob/master/setup.py#L35)) to decide that. I am wanting to rewrite them all so that they will be easily copy and pasteable, which will make it simpler when I am testing the examples.

---

## Comment 2

> Username: pfultz2  
> Created at: 2016-03-26 02:10:22 UTC  
> Url: https://github.com/boostorg/hof/issues/144#issuecomment-201669844  

All the examples in the reference are now copy and pasteable. The tutorial doesn't have them although I am thinking about pulling them directly from the example directory. I am thinking about switching to sphinx for documentation, which might make this easier. I am going to close this for now.
