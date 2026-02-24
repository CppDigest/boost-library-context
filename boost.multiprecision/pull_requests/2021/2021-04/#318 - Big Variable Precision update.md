# #318 Big Variable Precision update. [Merged]

> Username: jzmaddock  
> Created at: 2021-04-05 18:03:03 UTC  
> Updated at: 2021-06-27 08:41:33 UTC  
> Merged at: 2021-06-27 08:41:33 UTC  
> Closed at: 2021-06-27 08:41:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/318  

Update precision guards to account for the precision of *this.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-04-05 18:05:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/318#issuecomment-813544383  

Not such a big update yet, just baby steps so far.  This will eventually fix https://github.com/boostorg/multiprecision/issues/308

---

## Comment 2

> Username: ofloveandhate  
> Created_at: 2021-04-27 22:51:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/318#issuecomment-827998799  

I just tried compiling my tests using this branch at commit 10be26955862d19b94049a268d7bc9b190e409d3.  I got to the linking stage, and received an error from the linker:  
```  
ld: illegal thread local variable reference to regular symbol __ZZN5boost14multiprecision8backends6detail15mpc_complex_impILj0EE21get_default_precisionEvE3val for architecture x86_64  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
```  
I'm on MacOS 11.3, with the following for my clang version:  
```  
clang --version  
Apple clang version 12.0.0 (clang-1200.0.32.29)  
Target: x86_64-apple-darwin20.4.0  
Thread model: posix  
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
```  
So, the thread local storage is an issue on my current development platform.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-04-28 08:08:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/318#issuecomment-828246001  

>I just tried compiling my tests using this branch at commit 10be269. I got to the linking stage, and received an error from the linker:  
  
Oh, that just sucks :(  
  
Are you building a shared library by any chance?  Googling around, I did find plenty of references to this error message, but only when using `__thread`, and the fix was to use `thread_local` !  What happens if you make the problem function non-inline?  
  
More big updates on this soon BTW, just not (yet) for this issue...

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2021-05-12 16:34:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/318#issuecomment-839926180  

@ofloveandhate: this should be worth a serious look now.  I still haven't sorted out the MacOS threading issue (not sure how to proceed on that yet), and I need to investigate setting the options at compile time.  
  
Documentation will read as follows:  
  
  
<!--StartFragment-->  
<p>  
        There are a number of types in this library whose precision can be changed  
        at runtime, the purpose of this section is to explain how these types interoperate  
        with each other when two variables of the same type can have different precisions.  
      </p>  
<p>  
        The main variable precision types being discussed here are:  
      </p>  
<div class="itemizedlist"><ul class="itemizedlist" style="list-style-type: disc; ">  
<li class="listitem">  
            <a class="link" href="file:///D:/data/boost/boost/libs/multiprecision/doc/html/boost_multiprecision/tut/floats/gmp_float.html" title="gmp_float">gmp_float</a>.  
          </li>  
<li class="listitem">  
            <a class="link" href="file:///D:/data/boost/boost/libs/multiprecision/doc/html/boost_multiprecision/tut/floats/mpfr_float.html" title="mpfr_float">mpfr_float</a>.  
          </li>  
<li class="listitem">  
            <a class="link" href="file:///D:/data/boost/boost/libs/multiprecision/doc/html/boost_multiprecision/tut/interval/mpfi.html" title="mpfi_float">mpfi_float</a>.  
          </li>  
<li class="listitem">  
            <a class="link" href="file:///D:/data/boost/boost/libs/multiprecision/doc/html/boost_multiprecision/tut/complex/mpc_complex.html" title="mpc_complex">mpc_complex</a>.  
          </li>  
</ul></div>  
<p>  
        Functions for setting the current working precision are as follows, with  
        type <code class="computeroutput"><span class="identifier">N</span></code> being one of <code class="computeroutput"><span class="identifier">mpf_float</span></code>, <code class="computeroutput"><span class="identifier">mpfr_float</span></code>,  
        <code class="computeroutput"><span class="identifier">mpfi_float</span></code> or <code class="computeroutput"><span class="identifier">mpc_float</span></code>, and <code class="computeroutput"><span class="identifier">val</span></code>  
        being an object of type <code class="computeroutput"><span class="identifier">N</span></code>:  
      </p>  
<div class="informaltable">  
  
Expression | Returns | Comments  
-- | -- | --  
val.precision() | unsigned | Returns the precision of variable val.  
val.precision(n) | void | Sets the precision of variable val                   to n decimal places.  
N::default_precision() | unsigned | Returns the current global default precision, in decimal digits                   - this is the precision that all new threads will inherit.  
N::thread_default_precision() | unsigned | Returns the current thread default precision, in decimal digits                   - this is the precision that the current thread will use when constructing                   new objects of type N.  
N::default_precision(Digits10) | void | Sets the global default precision to Digits10 decimal places, this                   is the precision that all new threads will inherit, also sets the                   working precision for the current thread.  
N::thread_default_precision(Digits10) | void | Sets the default precision for the current thread to Digits10 decimal                   places.  
  
<!--EndFragment-->  
  
 We must now consider what happens in an expression such as:  
  
```  
variable = some_expression;  
```  
  
There are basically 2 options here when the precision of variable and some_expression differ:  
  
*    We can preserve the precision of the source, so that post assignment, source and target are equal.  
*    We can preserve the precision of the target, so that the precision of variable doesn't change.  
  
In addition we must consider what happens if some_expression contains types other than N.  
  
The behaviour of the library is controlled by the following enumerated values:  
  
```  
namespace boost::multiprecision {  
  
   enum struct variable_precision_options  
   {  
      assume_uniform_precision = -1,  
      preserve_target_precision = 0,  
      preserve_source_precision = 1,  
      preserve_component_precision = 2,  
      preserve_related_precision = 3,  
      preserve_all_precision = 4,  
   };  
  
}  
```  
  
The enumerated values have the following meanings, with preserve_related_precision being the default:   
  
<!--StartFragment--><div class="informaltable">  
  
Value | Meaning  
-- | --  
assume_uniform_precision | This is the most efficient option - it simply assumes that all                   variables in the current thread have the same precision, and ignores                   the precision of all other types. Shoud these assumptions not hold,                   then strange unexpected things may happen. No checks are made to                   ensure that all variables really are of the same precision.  
preserve_target_precision | All expressions are evaluated at the precision of the highest precision                   variable within the expression, and then rounded to the precision                   of the target variable upon assignment. The precision of other                   types (including related or component types - see below) contained                   within the expression are ignored. This option has the unfortunate                   side effect, that moves may become full deep copies.  
preserve_source_precision | All expressions are evaluated at the precision of the highest precision                   variable within the expression, and that precision is preserved                   upon assignment. The precision of other types (including related                   or component types - see below) contained within the expression                   are ignored. Moves, are true moves not copies.  
preserve_component_precision | All expressions are evaluated at the precision of the highest precision                   variable within the expression, and that precision is preserved                   upon assignment. If the expression contains component types then                   these are also considered when calculating the precision of the                   expression. Component types are the types which make up the two                   components of the number when dealing with interval or complex                   numbers. They are the same type as N::value_type.                   Moves, are true moves not copies.  
preserve_related_precision | As above, but in addition to component types, all related types                   are considered when evaluating the precision of the expression.                   Related types are considered to be instantiations of the same template,                   but with different parameters. So for example mpfr_float_100                   would be a related type to mpfr_float,                   and all expressions containing an mpfr_float_100                   variable would have at least 100 decimal digits of precision when                   evaluated as an mpfr_float                   expression.  
preserve_all_precision | As above, but all types are considered when evaluating the precision                   of the expression. For example, if the expression contains an                   mpz_int, then the                   precision of the expression will be sufficient to store all of                   the digits in the integer unchanged. This option should generally                   be used with extreme caution, as it can easily cause unintentional                   precision inflation.  
  
</div>  
<p>  
        As with working precision, the above options can be set or queried on either  
        a global or thread-local level, note that these options can not be set on  
        a per-variable basis since they control whole expressions, not individual  
        variables:  
      </p><!--EndFragment-->  
  
<!--StartFragment-->  
  
Expression | Returns | Comments  
-- | -- | --  
N::default_variable_precision_options() | variable_precision_options | Returns the current global default options, these are the options                   that all new threads will inherit.  
N::thread_default_variable_precision_options() | variable_precision_options | Returns the options in use in the current thread when evaluating                   expressions containing type N.  
N::default_variable_precision_options(opts) | void | Sets the global default options to opts                   which must be one of the enumerated variable_precision_options                   values, this is setting that all new threads will inherit, also                   sets the options for the current thread.  
N::thread_default_variable_precision_options(opts) | void | Sets the options for the current thread to opts                   which must be one of the variable_precision_options                   enumerated values.  
  
<!--EndFragment-->

---

## Comment 5

> Username: ofloveandhate  
> Created_at: 2021-05-13 02:54:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/318#issuecomment-840252258  

This is a great step, John.  Thanks for working on this!  I haven't yet solved the thread local problem on macOS, so I'd really appreciate help on that one.

---

## Comment 6

> Username: ofloveandhate  
> Created_at: 2021-05-13 03:03:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/318#issuecomment-840255465  

I have some constructive criticism regarding the documentation.  I'm writing this with my college professor hat on, seeking to make the documentation as self-contained as possible, and increase accessibility.  
  
I think a big improvement would be the addition of examples.  Perhaps a table of variables at various precisions, and a column indicating how the precision policy affects the precision of an assignment after arithmetic.  I think that kind of visual comparison would help make the policies make more sense to a newcomer (and me, too!).  Also, if they have examples, then they don't have to write that starter code themselves.  A great gift would be a small amount of copy-pastable code that should run and give some expected results in terms of precisions of variables.    
  
There're a few places that use the phrase "as above" is used; I found those confusing, because there were multiple things above and I didn't know which one was being referred to at that moment.  I think that this documentation would benefit from making references within the documentation more explicit.  
  
Once I get to a state where I can compile this branch on my Mac, I can play around with the code and *maybe* even try to contribute to that documentation...

---

## Comment 7

> Username: ofloveandhate  
> Created_at: 2021-05-13 03:07:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/318#issuecomment-840257256  

for threading, is there a linker option I'm supposed to use?    
  
I use the autotools at the moment, and use the m4 macros for finding boost libraries.  Since Multiprecision is a header-only library, there's no provided script for finding boost.  So, I wrote my own (probably terrible) version.  Is this m4 script the right place to detect and set the option I'm supposed to use so that thread local works correctly?

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2021-05-15 15:28:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/318#issuecomment-841679966  

> I think a big improvement would be the addition of examples.  
  
Definitely, I just haven't got that far yet ;)  
  
> There're a few places that use the phrase "as above" is used; I found those confusing, because there were multiple things above and I didn't know which one was being referred to at that moment.   
  
Will do.  
  
> for threading, is there a linker option I'm supposed to use?  
  
I wouldn't think so.  
  
>I use the autotools at the moment, and use the m4 macros for finding boost libraries. Since Multiprecision is a header-only library, there's no provided script for finding boost. So, I wrote my own (probably terrible) version. Is this m4 script the right place to detect and set the option I'm supposed to use so that thread local works correctly?  
  
Hmmm, how about you run the tests locally and see what command line options are used, like so:  
  
```  
cd path-to-boost  
./bootstrap.sh  
# this line is for my benefit, it prints out compiler info:  
b2 libs/config/test//print_config_info  
cd libs/multiprecision/test  
# This line runs the precision-related tests and prints out all the commands used:  
../../../b2 -d2 precision_tests  
```  
  
Meanwhile, I note that there is still a thread-safety issue somewhere in the Boost.Math Bernoulli number code that I just can't track down at present....

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2021-06-01 18:56:32 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/318#issuecomment-852367482  

OK, I hope I'm about done here, fleshed out docs attached.  [variable _precision.pdf](https://github.com/boostorg/multiprecision/files/6579977/variable._precision.pdf)  
  
I still don't have a way to reproduce your OS X linking issue, that may have to be dealt with as a separate  
 issue depending on the cause.

---

## Comment 10

> Username: ofloveandhate  
> Created_at: 2021-06-01 21:40:57 UTC  
> Updated_at: 2021-06-01 21:41:11 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/318#issuecomment-852464156  

Great thanks!!!   I'm just getting back to my keyboard after a much-needed break.    
  
In the intervening time since your suggestion, and in trying to solve an issue with a different piece of software, I started over with XCode's command line tools on this computer.  Now my executables are linking correctly when compiled against this branch of Multiprecision -- I added no new linker options.  🤷🏻‍♀️  So if it resurfaces, we'll tackle it in a separate issue.

---

## Comment 11

> Username: ofloveandhate  
> Created_at: 2021-06-02 00:25:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/318#issuecomment-852593379  

I have a bit more feedback on the documentation:  
  
* In a tabular environment, there's "(see below)".  I think this would be more clear with a reference to a labeled entity on the page.  
* When you write "Note that the values  
`preserve_source_precision`, [...] form a hierarchy", I would really appreciate a visualization of this.  
* The symbol $N$ is used in two distinct ways on this page:  
  1. As the type of a number.  
  2. As the precision of a number.    
    
  I would really appreciate disambiguation.

---

## Comment 12

> Username: ofloveandhate  
> Created_at: 2021-06-02 01:12:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/318#issuecomment-852633469  

I'm following along with your tutorial, and I have the following code:  
  
```  
using mpfr_float = boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<0>, boost::multiprecision::et_on>;  
using mpz_int = boost::multiprecision::number<boost::multiprecision::backends::gmp_int, boost::multiprecision::et_on>;  
using mpq_rational = boost::multiprecision::number<boost::multiprecision::backends::gmp_rational, boost::multiprecision::et_on>;  
using mpc_complex = boost::multiprecision::number<boost::multiprecision::backends::mpc_complex_backend<0>, boost::multiprecision::et_on>;  
  
namespace utf = boost::unit_test;  
  
BOOST_AUTO_TEST_SUITE(boost_multiprecision_tutorial)  
  
  
using mp_t = boost::multiprecision::debug_adaptor_t<boost::multiprecision::mpfr_float>;  
  
struct scoped_mpfr_precision  
{  
	unsigned saved_digits10;  
	scoped_mpfr_precision(unsigned digits10) : saved_digits10(mp_t::thread_default_precision())  
	{  
		mp_t::thread_default_precision(digits10);  
	}  
  
	~scoped_mpfr_precision()  
	{  
		mp_t::thread_default_precision(saved_digits10);  
	}  
  
	void reset(unsigned digits10)  
	{  
		mp_t::thread_default_precision(digits10);  
	}  
  
	void reset()  
	{  
		mp_t::thread_default_precision(saved_digits10);  
	}  
};  
  
struct scoped_mpfr_precision_options  
{  
  
	boost::multiprecision::variable_precision_options saved_options;  
  
	scoped_mpfr_precision_options(boost::multiprecision::variable_precision_options opts) : saved_options(mp_t::thread_default_variable_precision_options())  
	{  
		mp_t::thread_default_variable_precision_options(opts);  
	}  
  
	~scoped_mpfr_precision_options()  
	{  
		mp_t::thread_default_variable_precision_options(saved_options);  
	}  
  
	void reset(boost::multiprecision::variable_precision_options opts)  
	{  
		mp_t::thread_default_variable_precision_options(opts);  
	}  
};  
  
  
  
BOOST_AUTO_TEST_CASE(precision_2_to_the_1000_minus_1_preserve_source)  
{  
	scoped_mpfr_precision scope_1(mp_t::thread_default_precision());  
	scoped_mpfr_precision_options scope_2(boost::multiprecision::variable_precision_options::preserve_source_precision);  
  
  
	// calculate 2^1000 - 1:  
	mpz_int i(1);  
	i = i << 1000;  
	i -= 1;  
	std::cout << i << std::endl;  
	mp_t f = i;  
  
	BOOST_CHECK(f.precision()>mp_t::thread_default_precision());  
}  
```  
  
---  
  
This test is "failing".  I'm confused -- since the precision option is APPoS, shouldn't `f` have the same precision as `i` after this assignment?  Also, how do I get the precision of `i`?

---

## Comment 13

> Username: ofloveandhate  
> Created_at: 2021-06-02 01:13:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/318#issuecomment-852633915  

Also, in your tutorial, I think the line `i << 1000;` needs to be `i = i << 1000;`

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2021-06-13 10:14:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/318#issuecomment-860186558  

Apologies for the delay in getting back to you - and many thanks for the comments!  
  
>In the intervening time since your suggestion, and in trying to solve an issue with a different piece of software, I started over with XCode's command line tools on this computer. Now my executables are linking correctly when compiled against this branch of Multiprecision -- I added no new linker options. 🤷🏻‍♀️ So if it resurfaces, we'll tackle it in a separate issue.  
  
OK, so there is a bug somewhere with some compiler options probably that we don't know what it is !  
  
>In a tabular environment, there's "(see below)". I think this would be more clear with a reference to a labeled entity on the page.  
  
Done.  
  
> When you write "Note that the values preserve_source_precision, [...] form a hierarchy", I would really appreciate a visualization of this.  
  
Done.  
  
>The symbol $N$ is used in two distinct ways on this page:  
>  
>    As the type of a number.  
>    As the precision of a number.  
  
Hmmm, I only see it used as a type, but it is used as a precision elsewhere, so changed to "Num" to be clearer.  
  
>Also, in your tutorial, I think the line i << 1000; needs to be i = i << 1000;  
  
Good spot!  Fixed.  
  
Updated docs:   
[vp.pdf](https://github.com/boostorg/multiprecision/files/6643682/vp.pdf)

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2021-06-13 10:17:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/318#issuecomment-860186952  

>	scoped_mpfr_precision scope_1(mp_t::thread_default_precision());  
  
The aim of a coped object is to manage a change: here you're "changing" to it's existing default value - ie doing nothing.  It does no harm of course.  
  
>	scoped_mpfr_precision_options scope_2(boost::multiprecision::variable_precision_options::preserve_source_precision);  
  
In order to capture the full precision of an integer - a completely unrelated type - you need preserve_all_precision, preserve_source_precision only considers types the same as the result.

---
