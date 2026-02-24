# #677 - Does it support std::optional or boost::optional out of the box ? [Closed]

> Username: vricosti  
> Created at: 2022-02-09 12:55:11 UTC  
> Updated at: 2022-10-17 14:13:08 UTC  
> Closed at: 2022-10-17 14:13:08 UTC  
> Url: https://github.com/boostorg/json/issues/677  

Hello,  
  
Does it support simple json conversion from `std::optional<std::string>` for instance ? When I test I get some errors.  
Thanks

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-02-09 13:32:29 UTC  
> Url: https://github.com/boostorg/json/issues/677#issuecomment-1033765502  

Not yet, but it will be most likely added in the future.

---

## Comment 2

> Username: toonetown  
> Created at: 2022-05-09 20:39:06 UTC  
> Url: https://github.com/boostorg/json/issues/677#issuecomment-1121559447  

I would like to request this as well.  Is this something that is being actively looked at, or is it something where a PR would be welcomed?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-05-09 22:55:36 UTC  
> Url: https://github.com/boostorg/json/issues/677#issuecomment-1121662174  

What would "supporting optional" look like? Can you please give example code (using types from Boost.JSON)?

---

## Comment 4

> Username: toonetown  
> Created at: 2022-05-09 23:21:01 UTC  
> Url: https://github.com/boostorg/json/issues/677#issuecomment-1121679456  

As I am looking into `boost::json` more, I think it might be achievable outside the library by treating `null` values as an optional with no value.  Perhaps supporting it inside the `boost::json` library is not necessary nor desirable.

---

## Comment 5

> Username: pdimov  
> Created at: 2022-08-03 07:48:26 UTC  
> Url: https://github.com/boostorg/json/issues/677#issuecomment-1203605287  

> What would "supporting optional" look like?  
  
Default implementations of `value_from` and `value_to` for `std::optional<T>`.

---

## Comment 6

> Username: grisumbras  
> Created at: 2022-08-03 16:02:02 UTC  
> Url: https://github.com/boostorg/json/issues/677#issuecomment-1204152924  

To be honest, the hardest part of implementing this feature is determining a good trait for what is and isn't an optional. I.e. is checking that the type is `std::optional<T>` good enough? I suspect people would expect `boost::optional` to be supported too.

---

## Comment 7

> Username: madmongo1  
> Created at: 2022-08-03 16:03:28 UTC  
> Url: https://github.com/boostorg/json/issues/677#issuecomment-1204154740  

Optionals have the member functions .has_value() and .get()  
That’s probably enough?

---

## Comment 8

> Username: grisumbras  
> Created at: 2022-08-03 16:06:01 UTC  
> Url: https://github.com/boostorg/json/issues/677#issuecomment-1204158122  

Yeah, could work

---

## Comment 9

> Username: pdimov  
> Created at: 2022-08-03 16:43:25 UTC  
> Url: https://github.com/boostorg/json/issues/677#issuecomment-1204213500  

`boost::optional` can be supported on the `boost::optional` side. There's no need for JSON to know about all the optionals.

---

## Comment 10

> Username: NexusHero  
> Created at: 2022-08-10 11:13:45 UTC  
> Url: https://github.com/boostorg/json/issues/677#issuecomment-1210527519  

Is there a timeline for this feature?

---

## Comment 11

> Username: grisumbras  
> Created at: 2022-08-11 05:12:52 UTC  
> Url: https://github.com/boostorg/json/issues/677#issuecomment-1211559291  

Most likely before the next Boost release (December)
