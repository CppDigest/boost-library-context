# #1564 - Consider using boost::filesystem::path in path_cat in the examples [Closed]

> Username: Eelis  
> Created at: 2019-04-10 21:19:40 UTC  
> Updated at: 2019-05-17 22:08:15 UTC  
> Closed at: 2019-05-17 22:08:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1564  

This would likely make `path_cat` a one liner, or remove the need for it entirely, making the examples shorter, simpler, and more correct.  
  
Some of these benefits can also be achieved by adding a comment instead that reads  
  
    // in real code, prefer using {boost,std}::filesystem::path's operator/  
  
That way someone reading the example immediately knows that there's nothing interesting or important going on in `path_cat`, and can safely skip reading it. :)

---

## Comment 1

> Username: stale[bot]  
> Created at: 2019-05-10 22:08:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1564#issuecomment-491443905  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: stale[bot]  
> Created at: 2019-05-17 22:08:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1564#issuecomment-493613211  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
