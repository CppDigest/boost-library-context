# #196 - C++23: Implement less strict end-of-line continuations [Open]

> Username: jefftrull  
> Created at: 2023-11-30 17:41:52 UTC  
> Updated at: 2023-11-30 17:41:52 UTC  
> Url: https://github.com/boostorg/wave/issues/196  

Following up on #195, @hkaiser [observes that](https://github.com/boostorg/wave/issues/195#issuecomment-1833765516) C++23 now allows arbitrary whitespace between the `\` and the newline. Currently Wave treats this case as an ordinary non-continued line.
