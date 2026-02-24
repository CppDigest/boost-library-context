# #23 - link=shared together with runtime-link=static is not allowed [Closed]

> Username: dAu6jARL  
> Created at: 2017-07-04 23:06:37 UTC  
> Updated at: 2017-07-09 06:49:57 UTC  
> Closed at: 2017-07-09 06:49:57 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/23  

rev: 8d11eeb3ade5963b4f85dc6dbc08586beec826d7.  
when using b2.exe in MSVC-14.1 as below, error occured.  
`b2 variant=debug,release link=static runtime-link=static threading=multi --with-stacktrace`  
  
> error: link=shared together with runtime-link=static is not allowed  
error: such property combination is either impossible   
error: or too dangerious to be of any use
