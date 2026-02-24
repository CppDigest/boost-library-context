# #355 X3: Use traits to test if container is empty [Merged]

> Username: timangus  
> Created at: 2018-01-24 17:30:45 UTC  
> Updated at: 2018-01-25 17:37:59 UTC  
> Merged at: 2018-01-24 22:50:46 UTC  
> Closed at: 2018-01-24 22:50:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/355  

::empty() is called on the Attribute in parse_into_container_impl. If Attribute has no ::empty(), if fails to compile. Probably instead it should use traits::is_empty(...).

---

## Comment 1

> Username: Kojoley  
> Created_at: 2018-01-24 17:42:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/355#issuecomment-360214930  

Do you specialize `traits::is_empty` for your custom container because it does not expose `empty` method (but use some other way to determine if it is empty)?  
I mostly see `traits::is_empty` is used to deal with `unused_type`, but `parse_into_container_impl::call` have override for it (added in ee4943d5891bdae0706fb616b908e3bf528e0dfa). Can you please provide a test case?

---

## Comment 2

> Username: timangus  
> Created_at: 2018-01-24 17:49:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/355#issuecomment-360217196  

Ah right, I'll admit I was testing using 1.66 rather than the develop branch; it looks like ee4943d would resolve my problem albeit in a different way. FWIW, my custom container is QString, and my traits are:  
  
```cpp  
namespace boost { namespace spirit { namespace x3 { namespace traits {  
  
template<>  
struct push_back_container<QString>  
{  
    template<typename T>  
    static bool call(QString& c, T&& val)  
    {  
        c.push_back(std::move(val));  
        return true;  
    }  
};  
  
template<>  
struct append_container<QString>  
{  
    template<typename Iterator>  
    static bool call(QString& c, Iterator first, Iterator last)  
    {  
        c.append(first, std::distance(first, last));  
        return true;  
    }  
};  
  
template<>  
struct is_empty_container<QString>  
{  
    static bool call(QString const& c)  
    {  
        return c.isEmpty();  
    }  
};  
  
}}}}  
```

---

## Comment 3

> Username: timangus  
> Created_at: 2018-01-24 17:55:22 UTC  
> Updated_at: 2018-01-24 17:56:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/355#issuecomment-360218870  

Hmm well, quickly patching ee4943d into my code doesn't fix the problem. It does seem like traits is the answer to be honest, it seems to have been in issue #287, whose fix is in the same function.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2018-01-24 18:00:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/355#issuecomment-360220526  

> It does seem like traits is the answer to be honest  
  
Yeah. I have asked the first question just to be sure you are using a custom container and we are not fixing some hidden problem with the patch.

---

## Comment 5

> Username: timangus  
> Created_at: 2018-01-24 18:01:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/355#issuecomment-360220701  

Ah right, cool. Thanks.

---

## Comment 6

> Username: djowel  
> Created_at: 2018-01-24 22:20:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/355#issuecomment-360293301  

👍

---

## Comment 7

> Username: Kojoley  
> Created_at: 2018-01-24 22:51:25 UTC  
> Updated_at: 2018-01-24 22:51:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/355#issuecomment-360301069  

Thanks for patch, and for Tremulous!

---

## Comment 8

> Username: timangus  
> Created_at: 2018-01-25 10:38:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/355#issuecomment-360428261  

Haha, you're welcome! Believe it or not a small number of people still play it.

---

## Comment 9

> Username: Kojoley  
> Created_at: 2018-01-25 17:37:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/355#issuecomment-360541769  

Nice to hear this! I had not played it since 2011 because of server population drop (it was hard to gather together even 2x2).

---
