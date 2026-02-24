# #68 Enable automated testing & fix compiler errors [Closed]

> Username: CromwellEnage  
> Created at: 2019-01-01 20:35:25 UTC  
> Updated at: 2019-01-16 03:57:37 UTC  
> Closed at: 2019-01-02 14:28:37 UTC  
> Url: https://github.com/boostorg/log/pull/68  

<boost/log/sinks/text_file_backend.hpp>  
* Use BOOST_PARAMETER_BASIC_FUNCTION() to generate aux::make_collector() overloads that accept named parameters in accordance with documentation.  
  
<boost/log/utility/setup/console.hpp>  
* Use mpl::has_key<> metafunction vice is_void<parameter::binding<> >.  
  
<boost/log/utility/setup/file.hpp>  
* Use mpl::has_key<> metafunction vice is_void<parameter::binding<> >.  
* Switch overload implementations based on usage of mpl::has_key<> metafunction vice is_void<parameter::binding<> >.  
* Correct usage of parameter::aux::tag<> metafunction.  (This fix is temporary.)  
  
<boost/log/detail/sink_init_helpers.hpp>  
* Switch overload implementations based on usage of mpl::has_key<> metafunction vice is_void<parameter::binding<> >.  
  
“example/doc/Jamfile.v2”  
* Disable test that fails on mingw.  
  
“.travis.yml”  
“appveyor.yml”  
* Enable automated testing.

---

## Comment 1

> Username: Lastique  
> Created_at: 2019-01-02 00:04:29 UTC  
> Url: https://github.com/boostorg/log/pull/68#issuecomment-450767033  

I don't want to use `BOOST_PARAMETER_BASIC_FUNCTION` to declare functions. Its syntax is too obscure and AFAIR it involves more expensive Boost.Parameter facilities compared to `keyword`. I assume, the other changes are related.  
  
If there is a particular problem you're trying to solve, could you describe it?

---

## Comment 2

> Username: CromwellEnage  
> Created_at: 2019-01-02 01:54:46 UTC  
> Url: https://github.com/boostorg/log/pull/68#issuecomment-450773098  

The other changes are not related.  This particular change is optional, so I've reverted it.  
  
That being said, I'm attempting to introduce a set of far less expensive code generation macros: <https://github.com/boostorg/parameter/pull/46>.  They provide a public alternative to the facilities defined in the header file <boost/log/detail/parameter_tools.hpp>.

---

## Comment 3

> Username: CromwellEnage  
> Created_at: 2019-01-02 02:22:16 UTC  
> Url: https://github.com/boostorg/log/pull/68#issuecomment-450777140  

Meanwhile, I'm looking to improve the Boost.Parameter interface, without breaking existing code, of course.  What kind of syntax would you prefer?

---

## Comment 4

> Username: Lastique  
> Created_at: 2019-01-02 09:39:59 UTC  
> Updated_at: 2019-01-02 09:43:44 UTC  
> Url: https://github.com/boostorg/log/pull/68#issuecomment-450820250  

> The other changes are not related.  
  
Then could you describe why the other changes are needed? Something changed in Boost.Parameter?  
  
> What kind of syntax would you prefer?  
  
The syntax of a C++ function declaration. :) Which I already have, barring the need to compose argument pack from function arguments. If you're looking to improve Boost.Parameter for Boost.Log, see: https://svn.boost.org/trac10/ticket/7247  
  
Also, I don't like the change to the Jamfile. I would rather keep everything compiling and try working around the compilation issue in the code. What compile error do you have?

---

## Comment 5

> Username: CromwellEnage  
> Created_at: 2019-01-02 13:56:25 UTC  
> Updated_at: 2019-01-02 13:56:43 UTC  
> Url: https://github.com/boostorg/log/pull/68#issuecomment-450869241  

> Then could you describe why the other changes are needed? Something changed in Boost.Parameter?  
  
Changes have been made to boost::parameter::aux::tag<>, a non-public metafunction.  
  
Your question seems to imply that you'd only rather see minimal changes, so that's exactly what I've reverted to.  Still, I'm not happy with the use of boost::parameter::aux::tag<> here.  
  
> > What kind of syntax would you prefer?  
  
> The syntax of a C++ function declaration. :) Which I already have, barring the need to compose argument pack from function arguments.  
  
The pull request I've submitted--<https://github.com/boostorg/parameter/pull/46>--also introduces:  
* the are_tagged_arguments<> variadic metafunction  
* a no-template-argument specialization of boost::parameter::parameters<> whose variadic function call operator composes its named arguments and returns the resulting parameter pack.  If the function call operator is invoked with no arguments, it returns an empty parameter pack.  
  
If and when the pull request is accepted and merged, can you make use of the second new facility without more internals being made public?  
  
> Also, I don't like the change to the Jamfile. I would rather keep everything compiling and try working around the compilation issue in the code. What compile error do you have?  
  
There was an error in the MinGW testing job, but it seems to have disappeared now.  Oh, well.

---

## Comment 6

> Username: Lastique  
> Created_at: 2019-01-02 14:04:30 UTC  
> Url: https://github.com/boostorg/log/pull/68#issuecomment-450871064  

I'm intentionally not using `parameters` template because it is more expensive to use compared to `arg_list` and offers features I don't need. At least, that was the case last time I checked.  
  
Compile time performance is very important for Boost.Log as it uses Boost.Parameter very widely.

---

## Comment 7

> Username: Lastique  
> Created_at: 2019-01-02 14:28:37 UTC  
> Url: https://github.com/boostorg/log/pull/68#issuecomment-450876776  

I've applied the change in 5434040, thanks. I will take care of CI separately later.

---

## Comment 8

> Username: Lastique  
> Created_at: 2019-01-02 14:34:24 UTC  
> Url: https://github.com/boostorg/log/pull/68#issuecomment-450878179  

> Still, I'm not happy with the use of `boost::parameter::aux::tag<>` here.  
  
For the record, I'm also not happy about using Boost.Parameter internals in Boost.Log. Hence my ticket about exposing some of them to users. I don't mind updating Boost.Log to use public alternatives, but only if they introduce bare minimum dependencies and compile-time and run-time overhead. My questions earlier were not meant as a rejection of the changes but rather an attempt to understand them, why they are needed, and what they entail.

---

## Comment 9

> Username: CromwellEnage  
> Created_at: 2019-01-02 14:48:09 UTC  
> Url: https://github.com/boostorg/log/pull/68#issuecomment-450881834  

> For the record, I'm also not happy about using Boost.Parameter internals in Boost.Log. Hence my ticket about exposing some of them to users. I don't mind updating Boost.Log to use public alternatives, but only if they introduce bare minimum dependencies and compile-time and run-time overhead. My questions earlier were not meant as a rejection of the changes but rather an attempt to understand them, why they are needed, and what they entail.  
  
Understood.  For my part, I'm quite happy to address your concerns, which I also share since I've experienced them especially while testing with older compilers.  
  
I've looked to <boost/log/detail/parameter_tools.hpp> for guidance in designing the new macros as well as the no-template-argument parameters<> specialization.  They're as bare-bones as I can make them, compile much faster than their existing counterparts, enable functions and member functions and function call operators as well as constructors, and should also minimize run-time overhead, though I don't yet have a proper benchmark test for that.

---

## Comment 10

> Username: Lastique  
> Created_at: 2019-01-02 15:00:08 UTC  
> Url: https://github.com/boostorg/log/pull/68#issuecomment-450885167  

Ok, thanks, I'll have to take another look at `parameters<>` then.

---

## Comment 11

> Username: Lastique  
> Created_at: 2019-01-02 15:31:14 UTC  
> Url: https://github.com/boostorg/log/pull/68#issuecomment-450893841  

Looking at the current `parameters<>` implementation, it still has to go through the `make_arg_list` hoop to convert to the actual `arg_list`, but in C++11 it now also depends on Boost.Fusion. Compared to dealing directly with `arg_list`, which gets built automatically by using operators on keywords, this is a lot of overhead used for no gain in the context of Boost.Log. So for now I'm going to stick with `arg_list`, hoping that the missing bits will become public.

---

## Comment 12

> Username: Lastique  
> Created_at: 2019-01-16 00:11:39 UTC  
> Url: https://github.com/boostorg/log/pull/68#issuecomment-454600804  

@CromwellEnage I've merged this change to master and now the CI [fails](https://ci.appveyor.com/project/Lastique/log/builds/21628319/job/dbdseb9nhl3kb2te?fullLog=true#L1616). I'm assuming, you haven't merged your changes to master yet?

---

## Comment 13

> Username: CromwellEnage  
> Created_at: 2019-01-16 03:57:37 UTC  
> Url: https://github.com/boostorg/log/pull/68#issuecomment-454643472  

@Lastique, Sorry, I don't have the authority to merge from develop to master for Parameter.

---
