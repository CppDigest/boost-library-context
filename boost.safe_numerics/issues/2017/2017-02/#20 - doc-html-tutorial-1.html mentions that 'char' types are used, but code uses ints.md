# #20 - doc/html/tutorial/1.html mentions that 'char' types are used, but code uses ints. [Closed]

> Username: biocomp  
> Created at: 2017-02-18 08:47:28 UTC  
> Updated at: 2018-09-23 22:26:35 UTC  
> Closed at: 2018-08-10 22:31:17 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/20  

The tutorial in the end says:   
Note that I've used char types in this example to make the problem and solution easier to see. The exact same example could have been done with int types albeit with different values.  
  
But the code does use ints.

---

## Comment 1

> Username: robertramey  
> Created at: 2017-02-18 17:56:02 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/20#issuecomment-280862901  

On 2/18/17 12:47 AM, Artem Tokmakov wrote:  
>  
> The tutorial in the end says:  
> Note that I've used char types in this example to make the problem and   
> solution easier to see. The exact same example could have been done   
> with int types albeit with different values.  
>  
> But the code does use ints.  
>  
Good catch.  My previous version used std::int8_t so it would be easier   
to see the values.  But it turns out that using this type didn't fail in   
all the places int did so I had to change the example.  I'm tweaking the   
narrative to match the current example.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/robertramey/safe_numerics/issues/20>, or mute the   
> thread   
> <https://github.com/notifications/unsubscribe-auth/AB4R3At_D31SlIUaiNl1LnUkeTP5Kkuhks5rdrAggaJpZM4MFDP7>.  
>  
  
--   
Robert Ramey  
www.rrsd.com  
(805)569-3793
