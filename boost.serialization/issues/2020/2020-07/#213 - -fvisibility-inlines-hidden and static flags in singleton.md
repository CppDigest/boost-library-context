# #213 - -fvisibility-inlines-hidden and static flags in singleton [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2020-07-28 16:35:21 UTC  
> Updated at: 2020-07-28 18:21:10 UTC  
> Closed at: 2020-07-28 17:36:22 UTC  
> Url: https://github.com/boostorg/serialization/issues/213  

Hi,  
  
While backporting some of the singleton changes from 1.69.0 to our own copy of Boost 1.68.0, I did notice the the newly introduced class `boost::serialization::detail::singleton_wrapper` add no symbol exposed at all. Meaning that the `is_destroyed` flag isn't visible to other Linux DSO's instanting singleton of a type already instantiated by another DSOs. Same for the gcc's guard symbol variable for the static variables declared in functions. So basically I doubt that in presence of DSOs (unlike static libraries), your singletons actually are singletons: you can have one singleton instantiation per DSO for the same type, resulting in multiple copies of the singleton (and also multiple flags `is_destroyed` for the same class).  
  
I guess this comes from:  
 - your lack of using `BOOST_SYMBOL_VISIBLE` in `singleton_wrapper`  
 - even with `BOOST_SYMBOL_VISIBLE` used, it looks like `-fvisibility-inlines-hidden` actually overrides it and make then hidden anyway. Using `BOOST_DLLEXPORT` doesn't do anything for Linux as the macro is empty there.  
  
Is it really what you want ?  
  
Cheers,  
Romain

---

## Comment 1

> Username: robertramey  
> Created at: 2020-07-28 17:08:57 UTC  
> Url: https://github.com/boostorg/serialization/issues/213#issuecomment-665162910  

I've struggled with this.  I eventually concluded that my "singleton" has to be a singleton relative to the execution module.  So there are going to be multiple singletons in the program - one for the main exe and one for each dll/shared_library.  This is because shared libraries and be loaded/unloaded at will.  So the serialization code included in a shared library can be loaded/unloaded as well.  So the singleton - which holds per_type information matches the actual state of the shared library.  It's not great - but I felt its what I had to do to make things work.  This goes back the original design constraints which imply keeping type information around.  It's way to tricky - but you know when cramming in the last piece of a very complex jigsaw puzzle one is tempted to use the snips to make it work ... .  It's not really exactly right - but it seems the best of my options at this point.   
  
Oh - then after all this, included visibility macros - which aren't part of the standard so they're different for different compilers.  Another layer of agony.  
  
I would suggest that backporting stuff like this is not a great idea.  why not just upgrade to a later version of boost - 1.69, 1.70, etc.  The serialization library is pretty well maintained and generally each version is better than the previous one.

---

## Comment 2

> Username: Romain-Geissler-1A  
> Created at: 2020-07-28 17:35:07 UTC  
> Url: https://github.com/boostorg/serialization/issues/213#issuecomment-665176211  

Thanks for the info. I will close this issue if that's expected.  
  
> I would suggest that backporting stuff like this is not a great idea. why not just upgrade to a later version of boost - 1.69, 1.70, etc. The serialization library is pretty well maintained and generally each version is better than the previous one.  
  
It depends on the constraints you have to follow. My constraints are that I have to provide software where minor/patch releases remain both backward source compatible, and backward binary compatible. While Boost is usually quite source backward compatible from one release to another, it definitely is not backward binary compatible (starting with the SONAME which changes with every release). While many companies do rely on static linking, we do rely on dynamic linking, so SONAMEs do matter. And even without that, we have no guarantee that a lib will have deleted some symbols we used to use.  
For us, the cost of backporting this patch (a few hours to read the past discussion you had when you faced it, understand it, and adapt it to not break binary compatibility) clearly minors the cost we would need to rebuild all our binaries and maybe upgrade our source code to boost 1.69.0 (we are litterally talking man years of global effort for my company). Let's say as an analogy that my work in my company is like being the Debian maintainer of  the Boost packages: I can't change the Debian version of the already released Debian Buster version, but I can change the future version of the future Debian Bullseye. In the meantime I have real users having real core dump in their code today, which I need to fix without impacting all the others.

---

## Comment 3

> Username: robertramey  
> Created at: 2020-07-28 18:21:10 UTC  
> Url: https://github.com/boostorg/serialization/issues/213#issuecomment-665199918  

Sounds like you know what your doing.  I believe that most of boost is backward compatible at the source level.  Of course some function signatures might change so ABI compatibility isn't there.  I'm pretty sure that's the case with the serialization library.  At least that's what I strive for.
