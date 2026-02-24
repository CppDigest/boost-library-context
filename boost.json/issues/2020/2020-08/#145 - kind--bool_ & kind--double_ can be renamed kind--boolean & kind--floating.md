# #145 - kind::bool_ & kind::double_ can be renamed kind::boolean & kind::floating [Closed]

> Username: sdkrystian  
> Created at: 2020-08-10 21:54:05 UTC  
> Updated at: 2020-08-20 22:25:00 UTC  
> Closed at: 2020-08-20 22:24:59 UTC  
> Url: https://github.com/boostorg/json/issues/145  



---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-08-10 22:56:48 UTC  
> Url: https://github.com/boostorg/json/issues/145#issuecomment-671630024  

The reason I named them `bool_` and `double_` is because it resembles the actual C++ type as closely as possible

---

## Comment 2

> Username: sdkrystian  
> Created at: 2020-08-12 16:12:48 UTC  
> Url: https://github.com/boostorg/json/issues/145#issuecomment-672970298  

That's good, but they are a bit... ugly. Trailing underscores are most often used for private members, so it just doesn't look right.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-08-12 16:33:22 UTC  
> Updated at: 2020-08-12 16:33:45 UTC  
> Url: https://github.com/boostorg/json/issues/145#issuecomment-672981760  

Yes I know what you mean. It isn't perfect. But the universe isn't perfect. Here are three ways of naming the bool constant:  
  
1. `kind::bool`  
2. `kind::bool_`  
3. `kind::boolean`  
  
1 is of course the best but also a non-starter since it uses a reserved keyword. That leaves 2 and 3. Number 2 more closely resembles the keyword as it is the same as the keyword just with an underscore appended to it. Number 3 lacks the underscore but requires thought.  
  
For the double, things are more clear:  
  
1. `kind::double`  
2. `kind::double_`  
3. `kind::floating`  
  
As before, 1 is ideal but also a reserved keyword and thus unusable. Number 2 is the same name as the type but with an (ugly) underscore appended. Number 3 is really odd here because it is not the word double. Instead it is the word "floating" which is only distantly related to the type it describes.  
  
I think there is a strong case to be made that the currently chosen set of identifiers is the best. The "rules" for figuring out the name of a kind are very simple:  
  
1. write the type that you want (e.g. `int64`, `string`)  
2. if that type is a reserved keyword, append an underscore  
  
Now compare that with your proposed rules:  
  
1. write the type that you want, unless  
2. if that type is `bool` then write `boolean`, otherwise  
3. if that type is `double` then write `floating`  
  
Your rules require memorizing more trivia than the current rules.  
  
I think my case is strong, but sometimes there are naming objections or suggestions which come up during a Boost formal review. During the formal review period, if you still feel that the names should be changed, then you should bring this up and link this issue so that others can weigh in on it.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-08-20 22:24:59 UTC  
> Url: https://github.com/boostorg/json/issues/145#issuecomment-677937590  

Nothing actionable here
