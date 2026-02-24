# #45 - Make manual macro annotation unnecessary for simple aggregates, integrating Boost PFR [Open]

> Username: denzor200  
> Created at: 2023-12-11 21:40:05 UTC  
> Updated at: 2024-04-01 14:42:15 UTC  
> Url: https://github.com/boostorg/describe/issues/45  

Hi, @pdimov ! Let me introduce you a nice thing that I contribute for a long time.  
The [Boost PFR](https://github.com/boostorg/pfr) library provides everithing we need to get rid of `BOOST_DESCRIBE_STRUCT` macro(including extraction of fields names) and might be used in a lot of libraries that already relies of Boost Describe - Boost MySQL, Boost Json, etc.  
It seemed nice to integrate PFR as yet another reflection library near the Describe into all of these libraries, but wait don't we have a more shorter way? Why don't we integrate PFR into Describe, keeping the old interface but without macro? And then all of these Describe-relied libraries will automatically get Boost PFR support for user defined types.  
  
Of course we should bear in mind that PFR have limitations: https://www.boost.org/doc/libs/master/doc/html/boost_pfr/limitations_and_configuration.html  
  
What do you think about such development? Does it worth to start working on it? I believe it's possible, but does it fit Describe's conception?   
Please let me know if you are interested and don't hesitate to ask any question about Boost PFR.

---

## Comment 1

> Username: pdimov  
> Created at: 2023-12-12 00:00:03 UTC  
> Url: https://github.com/boostorg/describe/issues/45#issuecomment-1851086753  

I'm familiar with PFR and that it recently got the ability to extract field names under C++20 (that was your contribution, right?) but even with the names, it still doesn't match what Describe provides in general, because it only supports nonstatic data members (and I'm not sure there's a way to support mod_inherited properly).

---

## Comment 2

> Username: denzor200  
> Created at: 2023-12-12 20:03:03 UTC  
> Url: https://github.com/boostorg/describe/issues/45#issuecomment-1852721065  

Yeah, it was me contributed that feature  
  
> it still doesn't match what Describe provides in general  
  
You mean Describe provides only pointers to members, not regular pointers? That's not the issue indeed, PFR will support extracting member pointers very soon. Or you mean anything else?  
  
> I'm not sure there's a way to support mod_inherited properly  
  
PFR doesn't support inherited structures, that's the limitation. Those Describe's users who need inheritance will still use the macro. For a structure that doesn't have bases and fits all the rest of limitation no reason to use macro. That's the strategy I suggest  
  
Thanks for the quick answer!

---

## Comment 3

> Username: denzor200  
> Created at: 2023-12-12 20:08:32 UTC  
> Url: https://github.com/boostorg/describe/issues/45#issuecomment-1852728811  

I can contribute this feature for Describe if you are too busy to do it by yourself, that's not problem for me

---

## Comment 4

> Username: pdimov  
> Created at: 2023-12-12 23:42:48 UTC  
> Url: https://github.com/boostorg/describe/issues/45#issuecomment-1853037257  

I mean, if you have  
  
```  
struct X  
{  
    int x;  
};  
  
BOOST_DESCRIBE_STRUCT(X, (), (x))  
  
struct Y: X  
{  
    int y;  
    void f();  
  
    static int s;  
};  
  
BOOST_DESCRIBE_STRUCT(Y, (X), (y, f, s))  
```  
  
(https://godbolt.org/z/eWsv69vhM)  
  
Boost.Describe can give you descriptors for all the members of Y (including X::x with mod_inherited), but PFR doesn't work at all. We can make it work for simple structs like `X`, but even then, it will be unable to reflect static members or member functions if present.

---

## Comment 5

> Username: denzor200  
> Created at: 2023-12-13 06:29:15 UTC  
> Url: https://github.com/boostorg/describe/issues/45#issuecomment-1853340737  

Sure, those who need to reflect static members or member functions will continue using the macro, same for inherited structs. Is this unacceptable?

---

## Comment 6

> Username: kiwixz  
> Created at: 2024-03-25 13:14:56 UTC  
> Url: https://github.com/boostorg/describe/issues/45#issuecomment-2017985301  

It would be lovely to have this feature.  Even if opt-in with a macro (something like `BOOST_DESCRIBE_AGGREGATE(X)`) it's one less repeat of all the members names that I always forget to keep in sync!

---

## Comment 7

> Username: pdimov  
> Created at: 2024-03-25 13:56:51 UTC  
> Url: https://github.com/boostorg/describe/issues/45#issuecomment-2018065334  

Opt-in with a macro sounds doable, we'll be able to supply the base classes this way, but we still need the member pointers.

---

## Comment 8

> Username: pdimov  
> Created at: 2024-04-01 14:42:14 UTC  
> Url: https://github.com/boostorg/describe/issues/45#issuecomment-2029866562  

> That's not the issue indeed, PFR will support extracting member pointers very soon.  
  
Out of curiosity, does this work, and if so, how is it implemented?
