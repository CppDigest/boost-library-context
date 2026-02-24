# #991 - Allow unknown described class members with a parse option [Closed]

> Username: kiwixz  
> Created at: 2024-03-15 16:24:52 UTC  
> Updated at: 2024-10-24 12:52:56 UTC  
> Closed at: 2024-10-24 12:52:56 UTC  
> Url: https://github.com/boostorg/json/issues/991  

Hi, I'd like to make the parsing of my JSON config file more or less compatible across versions.  
  
If a new field gets added I can easily add it if not present in the `json::value` before parsing it to make sure `value_to` has anything it needs.  But a parse option to default construct in this case instead of erroring would not hurt.  
  
In a more complex case, if the config gets updated before the application parsing it `value_to` will complain because of size mismatch (see [here](https://github.com/boostorg/json/blob/37e3fd03cae75f151b4f1ecf1df817be61758d11/include/boost/json/detail/value_to.hpp#L426)).  I'd like to disable this check with a parse option, because it's much harder to iterate all the described classes to make sure there are no extra properties in the JSON.

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-03-15 17:04:18 UTC  
> Url: https://github.com/boostorg/json/issues/991#issuecomment-2000083922  

So, you are suggesting a runtime option, not another type trait?

---

## Comment 2

> Username: kiwixz  
> Created at: 2024-03-15 17:08:43 UTC  
> Url: https://github.com/boostorg/json/issues/991#issuecomment-2000091001  

I think this makes more sense as a runtime option, it's not that the type is special but rather that I don't care when parsing it.  Like `allow_comments` and friends.

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-03-15 17:13:38 UTC  
> Url: https://github.com/boostorg/json/issues/991#issuecomment-2000099091  

Do I understand it correctly, that you want to both allow missing fields, and allow extra fields? Or just the latter?

---

## Comment 4

> Username: grisumbras  
> Created at: 2024-03-15 17:15:30 UTC  
> Url: https://github.com/boostorg/json/issues/991#issuecomment-2000102194  

To allow missing fields our standard solution is to use optional. Would this work for you?

---

## Comment 5

> Username: kiwixz  
> Created at: 2024-03-15 17:19:45 UTC  
> Url: https://github.com/boostorg/json/issues/991#issuecomment-2000109217  

Allowing extra fields is more important to me, because its much more complicated to do beforehand and doesn't raise any questions on boost::json side.  
  
Allowing missing fields could be nice, but then you have to decide how to set them (letting them default-constructed seems an obvious solution).  
  
I already use optionals for null values, I didn't know it worked with missing values too!
