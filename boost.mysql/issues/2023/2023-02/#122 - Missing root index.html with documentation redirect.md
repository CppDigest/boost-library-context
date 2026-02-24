# #122 - Missing root index.html with documentation redirect [Closed]

> Username: glenfe  
> Created at: 2023-02-23 18:26:12 UTC  
> Updated at: 2023-02-23 18:36:37 UTC  
> Closed at: 2023-02-23 18:36:37 UTC  
> Url: https://github.com/boostorg/mysql/issues/122  

Every Boost library needs a documentation redirect in index.html in root. Notice that https://www.boost.org/doc/libs/master/libs/mysql/ doesn't actually show your documentation. It has to redirect to doc/html.  
  
Which is where your actual documentation is generated: https://www.boost.org/doc/libs/master/libs/mysql/doc/html/  
  
This needs to happen before the release. If not done by this Friday, I'll take care of it for you.
