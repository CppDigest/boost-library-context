# #288 - Windows CI builds failing because config has windows-latest but old msvc reference [Closed]

> Username: jeremy-murphy  
> Created at: 2022-02-19 20:00:36 UTC  
> Updated at: 2022-02-20 01:32:45 UTC  
> Closed at: 2022-02-20 01:32:45 UTC  
> Url: https://github.com/boostorg/graph/issues/288  

windows-latest changes over time and no longer has the version of MSVC that it is paired with in our config.  
Better to not use windows-latest unless there is an equivalent msvc-latest to pair it with.
