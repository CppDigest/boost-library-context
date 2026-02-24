# #200 - Emitted pragmas lack terminating newline [regression] [Closed]

> Username: jefftrull  
> Created at: 2024-01-18 00:09:28 UTC  
> Updated at: 2024-01-18 03:09:55 UTC  
> Closed at: 2024-01-18 03:09:55 UTC  
> Url: https://github.com/boostorg/wave/issues/200  

@rlenhardt observes [here](https://github.com/boostorg/wave/pull/191#discussion_r1450253001) that a fix from last year removed an "extra" newline that is syntactically necessary. The newline code should be restored and a unit test added to cover this situation.
