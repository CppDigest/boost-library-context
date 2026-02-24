# #49 - Failures because of lack of CI_SUPPRESS_KNOWN_ISSUES [Closed]

> Username: pdimov  
> Created at: 2017-09-07 17:45:30 UTC  
> Updated at: 2018-01-27 03:22:20 UTC  
> Closed at: 2018-01-25 12:01:04 UTC  
> Url: https://github.com/boostorg/type_traits/issues/49  

I've enhanced the superproject Appveyor (and Travis) to run the tests for the library updated in the commit:  
  
https://ci.appveyor.com/project/boostorg/boost/build/1.0.3623  
  
But type_traits fails because - I assume - CI_SUPPRESS_KNOWN_ISSUES isn't set.  
  
Since this Appveyor support is generic and not specific to a library, I'd rather not include library-specific workarounds in it such as this one; so maybe the test Jamfile could detect Appveyor and Travis using for example `[ os.environ CI ]` and turn on the workarounds automatically?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2017-10-25 18:11:29 UTC  
> Url: https://github.com/boostorg/type_traits/issues/49#issuecomment-339420668  

I've pushed a fix for this, also for building multiple configurations per virtual machine, and we'll see how it goes.

---

## Comment 2

> Username: Flast  
> Created at: 2018-01-27 03:00:47 UTC  
> Url: https://github.com/boostorg/type_traits/issues/49#issuecomment-360954906  

OFF TOPIC: @pdimov did you advertize about `CI_SUPPRESS_KNOWN_ISSUES` anywhere? I suppose many developer don't know about it (incl. me).

---

## Comment 3

> Username: pdimov  
> Created at: 2018-01-27 03:13:19 UTC  
> Updated at: 2018-01-27 03:15:00 UTC  
> Url: https://github.com/boostorg/type_traits/issues/49#issuecomment-360955653  

`CI_SUPPRESS_KNOWN_ISSUES` is a macro specific to TypeTraits's tests, defined by @jzmaddock here: https://github.com/boostorg/type_traits/blob/a6e879f0914e98db2c5d94fff2a33ce0539a06ab/test/Jamfile.v2#L12 and checked f.ex. here: https://github.com/boostorg/type_traits/blob/a6e879f0914e98db2c5d94fff2a33ce0539a06ab/test/tricky_rvalue_test.cpp#L22.  
  
There's nothing for me to advertise, really, as I've had nothing to do with it. :-)

---

## Comment 4

> Username: Flast  
> Created at: 2018-01-27 03:22:20 UTC  
> Url: https://github.com/boostorg/type_traits/issues/49#issuecomment-360956107  

> CI_SUPPRESS_KNOWN_ISSUES is a macro specific to TypeTraits's tests  
  
Ahhh, sorry I misunderstood that: I thought super-project defines it.
