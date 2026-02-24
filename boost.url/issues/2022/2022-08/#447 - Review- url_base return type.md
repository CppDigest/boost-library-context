# #447 - Review: url_base return type [Closed]

> Username: alandefreitas  
> Created at: 2022-08-23 20:33:53 UTC  
> Updated at: 2022-10-21 22:29:34 UTC  
> Closed at: 2022-10-21 22:29:34 UTC  
> Url: https://github.com/boostorg/url/issues/447  

Andrzej  
  
> Next, classes url_base and url_view_base are part of the library's public  
> API. Why? You can keep them as bases but not advertise them in the library  
> documentation. Do you expect any use cases for them?  
>   
  
Christopher  
  
> The url::set_... methods seem to follow a fluent api design which is  
nice, but unfortunately they return url_base&. So I can't construct a  
url inside a function call like:  
>  
>   
> httpClientGet(url{}.set_host(host).set_path("some/path"))  
>   
> Being able to add segments is great. Personally I'm a big fan of  
> overloading the /-operator to append paths like in the filesystem  
> library, because I think this results in more readable code. The example  
> of above could become:  
>   
> httpClientGet(url{host} / "some/path")  
  
> Hmm yeah I see what you mean. I didn't think of this - that's why  
> field experience is so vital. The reason they return url_base& is  
> because that's the class that has all the mutation logic. This is  
> shared between `url` and `static_url`. To get functions like  
> `set_host` to return `url&` instead would be challenging  
  
Sehe  
  
> I see a CRTP in your future (ew, but maybe sometimes warranted?)  
  
> This is too much of a disruptive and wholesale change for the syntax that it enables  
  
Peter  
  
> Returning url& is the right thing to do here. "That's too much disruption": not saying it isn't. As a user, I don't care how much work it is to implement the right thing.  
  
> "The problem is that static_url<40>{}.set_host(host) returns what?": should return static_url<40>&.  
  
> "That means that every modifier has to be redeclared for two more containers. If it could be scripted I would be more inclined, because keeping them in sync is a hassle.": If you expect them to change frequently, yes. In such a scenario, I just repeat the functions. Don't look for ways to avoid it because I don't use implementation inheritance. It's always more trouble than it's worth. It avoids the need to document the "base" classes that don't belong in the docs anyway. If you want read-only, you just use url_view which works for everything. And if you want mutable, well, a template will work.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-09-28 18:45:33 UTC  
> Url: https://github.com/boostorg/url/issues/447#issuecomment-1261326188  

Are we going to do anything about this?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-09-28 18:46:20 UTC  
> Url: https://github.com/boostorg/url/issues/447#issuecomment-1261326867  

idk man... I'm not really feeling it. We would have to duplicate every single function in the base class....

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-09-28 18:46:45 UTC  
> Url: https://github.com/boostorg/url/issues/447#issuecomment-1261327244  

we can leave it open for now and put it on a backlog or something, maybe when the library matures and we are not contemplating adding or changing any member functions, that's when we can do it

---

## Comment 4

> Username: alandefreitas  
> Created at: 2022-09-28 18:58:35 UTC  
> Url: https://github.com/boostorg/url/issues/447#issuecomment-1261340048  

> that's when we can do it  
  
Wouldn't it be an impossible breaking change at this point?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-09-28 19:31:11 UTC  
> Url: https://github.com/boostorg/url/issues/447#issuecomment-1261377031  

I don't think so, it only enables new functionality it doesn't break existing

---

## Comment 6

> Username: alandefreitas  
> Created at: 2022-09-28 19:52:58 UTC  
> Url: https://github.com/boostorg/url/issues/447#issuecomment-1261397767  

Isn't it changing the return type though?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2022-09-29 00:38:34 UTC  
> Url: https://github.com/boostorg/url/issues/447#issuecomment-1261606918  

Does the change in the return type break any code?
