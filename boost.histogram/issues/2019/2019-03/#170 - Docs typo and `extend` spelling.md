# #170 - Docs typo and `extend` spelling [Closed]

> Username: henryiii  
> Created at: 2019-03-09 23:07:59 UTC  
> Updated at: 2019-03-15 08:50:21 UTC  
> Closed at: 2019-03-15 08:03:39 UTC  
> Url: https://github.com/boostorg/histogram/issues/170  

The `extend` helper should be called `extent`, because it gets extents rather than extending anything.  
  
On the page [here](https://www.boost.org/doc/libs/develop/libs/histogram/doc/html/histogram/guide.html), `or to enable dynamic grow of the axis beyond the initial range.` should be `or to enable dynamic growth of the axis beyond the initial range.`  
  
The fact that an integer axis is shown for the no-overflow/underflow example caused me to make a mistake in setting up the template params for `regular` (regular has Transform, which requires an extra `bh::use_default`). Just thinking, it would be nice if there was a `bh::transform::use_default `, `bh::metadata::use_default`, etc. as the current method does not make it easy to read what each positional argument does.

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-03-15 07:59:32 UTC  
> Url: https://github.com/boostorg/histogram/issues/170#issuecomment-473192616  

Thank you for reporting this spelling error of "extend", I am very glad that we can still fix this before the release.  
  
I fixed the spelling error in the docs (there are more to be found, for sure).  
  
I understand your argument regarding `use_default`, but this goes against the idea behind using that type. `use_default` is defined in boost/core, so instead of `boost::histogram::use_default` you could also use `boost::use_default`. This type is a generic placeholder to signal the use of a default value without explicitly specifying it. Your suggestion would make it a specific placeholder again.  
The template parameters are documented in the reference:  
https://www.boost.org/doc/libs/develop/libs/histogram/doc/html/boost/histogram/axis/regular.html  
https://www.boost.org/doc/libs/develop/libs/histogram/doc/html/boost/histogram/axis/integer.html  
so it is possible to figure out what goes where.

---

## Comment 2

> Username: henryiii  
> Created at: 2019-03-15 08:38:36 UTC  
> Url: https://github.com/boostorg/histogram/issues/170#issuecomment-473202334  

Okay, since `boost::use_default` works, I’m fine with it. I prefer to reuse existing libraries and idioms when possible even with a small readability penalty; only having to learn use_default once for Boost is preferable.

---

## Comment 3

> Username: HDembinski  
> Created at: 2019-03-15 08:50:11 UTC  
> Updated at: 2019-03-15 08:50:21 UTC  
> Url: https://github.com/boostorg/histogram/issues/170#issuecomment-473205427  

I fully agree! `boost::use_default` used to be a type only for Boost.Iterator until very recently. I suggested to move it to Boost.Core.  
  
There is another reason why I don't want to change this: in C++17 everything will become much easier, because then you can just pass the "template parameters" as normal parameters to the constructor of the type. There will be deduction guides which generate the right kind of axis type. In other words, setting the options for the axes types will become much more natural, you won't have to see the template parameters explicitly anymore.
