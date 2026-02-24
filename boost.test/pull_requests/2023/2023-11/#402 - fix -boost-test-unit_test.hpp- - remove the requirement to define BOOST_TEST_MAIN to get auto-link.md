# #402 fix <boost/test/unit_test.hpp> - remove the requirement to define BOOST_TEST_MAIN to get auto-link [Closed]

> Username: gh-user-2022  
> Created at: 2023-11-29 09:32:13 UTC  
> Updated at: 2023-11-30 10:18:25 UTC  
> Closed at: 2023-11-30 10:18:24 UTC  
> Url: https://github.com/boostorg/test/pull/402  

This requirement coming from commit 961646222e4cf0eb475a86667a71ce89e4994d5c is obvious bug, because if user defines `BOOST_TEST_MAIN`, then boost defines `init_unit_test_suite` in `<boost/test/unit_test_suite.hpp>`, which conflicts with user's custom definition of `init_unit_test_suite`.  
  
(It's interesting however that it got "past radar" for 3+ years.)

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2023-11-29 09:57:31 UTC  
> Url: https://github.com/boostorg/test/pull/402#issuecomment-1831572796  

Thanks for raising the issue. Can you provide a scenario where we can see the issue?   
  
If I recall, this was quite complicated set up but it made sense at that time, and this part is related to auto-linking if my memory is correct. Auto-link affects shared/static lib, and the patch was the same as previous behaviour but narrowed only on the file that defined that macro, precisely to avoid several definitions of the `init_unit_test_suite`.

---

## Comment 2

> Username: gh-user-2022  
> Created_at: 2023-11-29 10:20:20 UTC  
> Url: https://github.com/boostorg/test/pull/402#issuecomment-1831609641  

The patch itself probably makes sense, except the part when it disables auto-link without `BOOST_TEST_MAIN`.  
  
The scenario is when auto-link is used and when the user defines custom `init_unit_test_suite`.  
  
Without this fix, it's necessary to define `BOOST_TEST_MAIN` to get linked the `libboost_unit_test_framework-vc142-mt-x64-1_83.lib`, but `BOOST_TEST_MAIN` also causes the boost to define `init_unit_test_suite` in `<boost/test/unit_test_suite.hpp>`, which conflicts with user's definition.  
  
The user-defined custom `init_unit_test_suite` is used to implement something like:  
  
```  
test_suite* init_unit_test_suite( int, char* [] )  
{  
    // ... parse command line arguments ...  
      
    // ... do some custom initialization ...  
      
    // add test cases  
    test_suite* test = BOOST_TEST_SUITE( "Test suite" );  
    test->add( BOOST_TEST_CASE( &Test1 ) );  
    if ( /* some condition */ ) {  
        test->add( BOOST_TEST_CASE( &Test2 ) );  
        test->add( BOOST_TEST_CASE( &Test3 ) );  
    }  
      
    // ...  
      
    return test;  
}  
```  
  
We have many unit tests structured like that, it was working for years.  
  
**Also, there seems to be no point (at all) to limit auto-link to one translation unit, even when user does not define custom `init_unit_test_suite`.**  
  
I can provide the reproducible example causing linker error, if necessary.

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2023-11-29 11:42:22 UTC  
> Url: https://github.com/boostorg/test/pull/402#issuecomment-1831736098  

I will answer to your points in a different order, sorry in advance if this is confusing.  
  
The change in this patch was necessary to be able to split big or long tests in several compilation units, otherwise the symbols get defined several times and the build fails. There is a sound rationale behind keeping this change.  
  
> Also, there seems to be no point (at all) to limit auto-link to one translation unit, even when user does not define custom init_unit_test_suite.  
  
As far as I remember, auto-link will emit a `pragma` that will instruct the linker to perform the link with an additional library, so doing that in one or many translation unit should not change anything. If I am missing something here, please let me know.  
  
Now, defining the `init_unit_test_suite` depends on how you are building your test module and plugging to the boost.test library: the procedure differs whether you are using the header, shared or static variant. Please note that `init_unit_test_suite` is already defined in the shared and static library, and having your own requires you to do additional steps. All those steps are explained in details in the documentation [here](https://www.boost.org/doc/libs/1_83_0/libs/test/doc/html/boost_test/adv_scenarios/test_module_init_overview.html) (and the 3 variants that page is linking to).  
  
For instance, if you decide to use the shared variant, you have to define `BOOST_TEST_NO_MAIN`, provide your own `main` from which you call your own `init_unit_test_suite`. See [here](https://www.boost.org/doc/libs/1_83_0/libs/test/doc/html/boost_test/adv_scenarios/shared_lib_customizations/entry_point.html). You *can't* provide a `init_unit_test_suite` without providing a `main` in this case. For the static case, it is even more complicated.  
  
As a final note, I see in your example that you are registering manually the test cases/suites: this is not needed and there are better ways to address conditions, such as [labels](https://www.boost.org/doc/libs/1_83_0/libs/test/doc/html/boost_test/tests_organization/tests_grouping.html) or [conditional test execution](https://www.boost.org/doc/libs/1_83_0/libs/test/doc/html/boost_test/tests_organization/enabling.html).  
  
I would recommand migrating your code to those facilities provided by the framework, the auto-linking, test module initialization etc where leading people to a very confusing architecture and way to use Boost.Test in the past (I include myself, I thought it was necessary until ~2013).  
  
I am happy to support you further in any migration work, and if I missed something please let me know: I am also happy to read the code that it causing you trouble.

---

## Comment 4

> Username: gh-user-2022  
> Created_at: 2023-11-29 16:34:17 UTC  
> Url: https://github.com/boostorg/test/pull/402#issuecomment-1832289091  

Regarding  
> As far as I remember, auto-link will emit a pragma that will instruct the linker to perform the link with an additional library, so doing that in one or many translation unit should not change anything. If I am missing something here, please let me know.  
  
This is correct.  
And that means that this change fixes our use case and has no effect (correct me if I'm wrong) on the use case that required the patch 961646222e4cf0eb475a86667a71ce89e4994d5c.  
Are you going to merge the fix?  
  
> I would recommand migrating your code to those facilities provided by the framework  
  
Regarding the migration, if a project has ~100 unit tests (some of which are quite large) structured that way, it's preferable to just keep Boost.Test compatible with that legacy code. (Especially if it requires to fix 1 line of code in Boost.)

---

## Comment 5

> Username: raffienficiaud  
> Created_at: 2023-11-29 20:30:17 UTC  
> Url: https://github.com/boostorg/test/pull/402#issuecomment-1832655252  

> Regarding  
>   
> > As far as I remember, auto-link will emit a pragma that will instruct the linker to perform the link with an additional library, so doing that in one or many translation unit should not change anything. If I am missing something here, please let me know.  
>   
> This is correct. And that means that this change fixes our use case and has no effect (correct me if I'm wrong) on the use case that required the patch [9616462](https://github.com/boostorg/test/commit/961646222e4cf0eb475a86667a71ce89e4994d5c).   
  
I explained the rationale of 961646222e4cf0eb475a86667a71ce89e4994d5c but maybe I or the commit message were not clear enough.  
  
It has an effect for the header only variant: in case you want to split a header-only test case into several units, the inclusion of `<boost/test/unit_test.hpp>` in any translation unit would, without 961646222e4cf0eb475a86667a71ce89e4994d5c, enforce the linker inclusion of the boost.test library.   
You can't include `<boost/test/included/unit_test.hpp>` in several translation units because this would also define the test entry point several time as well. With the commit 961646222e4cf0eb475a86667a71ce89e4994d5c enforce the activation of the auto-linking onto one and single one translation unit: the one that contains `BOOST_TEST_MAIN` which should be defined anyway.  
  
> Are you going to merge the fix?  
  
Since this breaks an existing use case / feature of Boost.Test, I don't believe it will make it.  
  
> > I would recommand migrating your code to those facilities provided by the framework  
>   
> Regarding the migration, if a project has ~100 unit tests (some of which are quite large) structured that way, it's preferable to just keep Boost.Test compatible with that legacy code. (Especially if it requires to fix 1 line of code in Boost.)  
  
It is up to you to keep and maintain a boost.test version containing this patch for the sake of compatibility with your code, or to keep using Boost.Test < 1.73. I am also pretty sure you can achieve the same thing in your code by adding the proper macros (maybe [this](https://www.boost.org/doc/libs/1_83_0/libs/test/doc/html/boost_test/utf_reference/link_references/link_boost_test_no_main.html) can help) and tweaking your build chain adequately.

---

## Comment 6

> Username: gh-user-2022  
> Created_at: 2023-11-30 10:18:24 UTC  
> Url: https://github.com/boostorg/test/pull/402#issuecomment-1833472032  

> pretty sure you can achieve the same thing in your code by adding the proper macros (maybe [this](https://www.boost.org/doc/libs/1_83_0/libs/test/doc/html/boost_test/utf_reference/link_references/link_boost_test_no_main.html) can help)  
  
There is currently no way to prevent definition of `init_unit_test_suite` when macro `BOOST_TEST_MAIN` is defined.  
  
> It is up to you to keep and maintain a boost.test version containing this patch  
  
Naturally. Such pull requests have purpose of reducing amount of patches.  
  
I may later submit another pull request, taking into account the mentioned _header only_ use case.  
Closing this one.

---
