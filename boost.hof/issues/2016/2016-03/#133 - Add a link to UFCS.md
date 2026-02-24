# #133 - [MoreExamples.Extension] Add a link to UFCS [Closed]

> Username: ldionne  
> Created at: 2016-03-11 15:35:35 UTC  
> Updated at: 2016-03-26 02:10:55 UTC  
> Closed at: 2016-03-26 02:10:55 UTC  
> Url: https://github.com/boostorg/hof/issues/133  

In the `Extension methods` subsection of the `More examples` section, you mention uniform function call syntax:  
  
> It would be nice to write this:  
>   
> ``` c++  
> auto r = numbers  
>    .filter([](int x) { return x > 2; })  
>    .transform([](int x) { return x * x; });  
> ```  
>   
> However, UFCS in C++17 won't allow this to be done generically. [...]  
  
It would be nice to have a link to the exact proposal you're referring to, and perhaps also add what _UFCS_  stands for. You assume that users will be aware of UFCS, which is probably not the case for the majority of people reading your documentation.
