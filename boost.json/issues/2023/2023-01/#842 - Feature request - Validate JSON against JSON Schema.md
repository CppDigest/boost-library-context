# #842 - Feature request - Validate JSON against JSON Schema [Open]

> Username: moravas  
> Created at: 2023-01-17 06:50:17 UTC  
> Updated at: 2024-06-02 13:51:36 UTC  
> Url: https://github.com/boostorg/json/issues/842  

Hi,  
  
In our Linux based C++ project we are using Boost JSON parsing library but I would like to validate the JSON document against JSON Schema.  
  
I don't see any support for schema validation in Boost JSON.  
  
Did I missed something, or is there any plan to add this feature to Boost?  
  
Thank you

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-01-17 06:53:30 UTC  
> Url: https://github.com/boostorg/json/issues/842#issuecomment-1384912928  

There's no JSON Schema support currently, and there's no current work to add it. But we often get asked about it, so this might change.

---

## Comment 2

> Username: moravas  
> Created at: 2023-01-28 08:24:32 UTC  
> Url: https://github.com/boostorg/json/issues/842#issuecomment-1407331043  

It would be a very welcomed feature. How can the community encourage you to raise the priority of this feature?  
  
Thank you!

---

## Comment 3

> Username: grisumbras  
> Created at: 2023-01-28 12:32:14 UTC  
> Url: https://github.com/boostorg/json/issues/842#issuecomment-1407389444  

Just commenting in this issue would be enough

---

## Comment 4

> Username: Superlokkus  
> Created at: 2023-04-25 08:50:31 UTC  
> Url: https://github.com/boostorg/json/issues/842#issuecomment-1521415521  

Also would like this feature, maybe I might be able to contribute.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2023-04-25 10:37:51 UTC  
> Url: https://github.com/boostorg/json/issues/842#issuecomment-1521564054  

> How can the community encourage you to raise the priority of this feature  
  
Well, you could write it, and submit a pull request...

---

## Comment 6

> Username: moravas  
> Created at: 2023-07-13 03:39:30 UTC  
> Url: https://github.com/boostorg/json/issues/842#issuecomment-1633499031  

Hi,  
Any news regarding this schema validator feature?  
  
Thank you

---

## Comment 7

> Username: grisumbras  
> Created at: 2023-07-17 14:54:39 UTC  
> Url: https://github.com/boostorg/json/issues/842#issuecomment-1638314396  

Nothing new yet.

---

## Comment 8

> Username: Superlokkus  
> Created at: 2023-07-17 17:50:08 UTC  
> Url: https://github.com/boostorg/json/issues/842#issuecomment-1638604367  

Hi moravas,  
I am currently reading into the boost JSON internals and the JSON Schema Draft but, as this is my first would be boost contrib. this will take a long while.

---

## Comment 9

> Username: nigels-com  
> Created at: 2024-05-05 23:12:27 UTC  
> Url: https://github.com/boostorg/json/issues/842#issuecomment-2094988565  

Is there enough interest in this to propose it as a GSOC '24 project?  
I know we'd use JSON Schema validation in C++, if it was available via boost.  
They're calling for mentors on the boost mailing list at the moment.

---

## Comment 10

> Username: grisumbras  
> Created at: 2024-05-06 06:34:37 UTC  
> Url: https://github.com/boostorg/json/issues/842#issuecomment-2095284619  

Depends on whether @vinniefalco considers this to be in scope.  
  
I initially thought that schema validation could be great in the example section. But I don't know if the implementation would be small enough to work as an example. I guess, if it is appropriate for a GSoC project, it's not so small.  
  
By the way, are there any other features related to JSON Schema, or is it only validation?

---

## Comment 11

> Username: nigels-com  
> Created at: 2024-05-06 08:14:31 UTC  
> Url: https://github.com/boostorg/json/issues/842#issuecomment-2095424294  

Indeed.  I don't really have a sense if such a thing should be part of boost::json, or an optional layer on top of it.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2024-05-06 16:47:43 UTC  
> Url: https://github.com/boostorg/json/issues/842#issuecomment-2096482161  

There is no rule that says examples can't be large.

---

## Comment 13

> Username: grisumbras  
> Created at: 2024-05-06 16:58:17 UTC  
> Url: https://github.com/boostorg/json/issues/842#issuecomment-2096500531  

The utility of an example greatly diminishes, if the implementation is too large to easily grok

---

## Comment 14

> Username: vinniefalco  
> Created at: 2024-05-06 17:56:53 UTC  
> Url: https://github.com/boostorg/json/issues/842#issuecomment-2096604437  

But that is false. The main use-case for examples is as follows: someone makes a copy of the code and incorporates it into their own program, or they use it as the beginning of a new program. For this purpose a large example is as good as any other.

---

## Comment 15

> Username: moravas  
> Created at: 2024-06-02 06:15:18 UTC  
> Url: https://github.com/boostorg/json/issues/842#issuecomment-2143715651  

Hi there,  
  
any progress?  
It would be a great feature in our product, where we process many JSON format text that in first step should fit into particular schema.  
  
I don't know other schema related feature right now

---

## Comment 16

> Username: grisumbras  
> Created at: 2024-06-02 13:51:35 UTC  
> Url: https://github.com/boostorg/json/issues/842#issuecomment-2143861904  

No work has been done on this front so far.
