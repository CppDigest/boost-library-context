# #77 Merge 'develop' changes [Merged]

> Username: CromwellEnage  
> Created at: 2019-04-27 12:07:51 UTC  
> Updated at: 2019-04-28 04:16:19 UTC  
> Merged at: 2019-04-28 04:16:19 UTC  
> Closed at: 2019-04-28 04:16:19 UTC  
> Url: https://github.com/boostorg/parameter/pull/77  

<ul>  
<li>Upgraded keyword generation macro <tt>BOOST_PARAMETER_TEMPLATE_KEYWORD</tt> (<a href=“https://github.com/boostorg/parameter/pull/15”>PR#15</a>).</li>  
<li>Moved keyword generation macro <tt>BOOST_PARAMETER_NESTED_KEYWORD</tt> from Boost.Accumulators to this library (<a href=“https://github.com/boostorg/parameter/pull/28”>PR#28</a>).</li>  
<li>Added support for <tt>std::reference_wrapper&lt;&gt;</tt> and <tt>std::ref()</tt> (<a href=“https://github.com/boostorg/parameter/pull/16”>PR#16</a>).</li>  
<li>Added support for passing functions as arguments to Boost.Parameter-enabled functions (<a href=“https://github.com/boostorg/parameter/pull/17”>PR#17</a>).</li>  
<li>Moved <tt>boost::parameter::required</tt>, <tt>boost::parameter::optional</tt>, and <tt>boost::parameter::deduced</tt> metafunction definitions to their own header files in directory boost/parameter (<a href=“https://github.com/boostorg/parameter/pull/18”>PR#18</a>).</li>  
<li>Added support for Boost.Parameter-enabled function call operators (<a href=“https://github.com/boostorg/parameter/pull/20”>PR#20</a>).</li>  
<li>Added support for parameter category qualifiers "forward”, "consume”, and "move_from" (current qualifiers are "in", "out", and "in_out") (<a href=“https://github.com/boostorg/parameter/pull/21”>PR#21</a>) (<a href=“https://github.com/boostorg/parameter/pull/23”>PR#23</a>) based on <a href=“http://www.modernescpp.com/index.php/c-core-guidelines-how-to-pass-function-parameters”>http://www.modernescpp.com/index.php/c-core-guidelines-how-to-pass-function-parameters</a>.  Added new usage syntax <tt>BOOST_PARAMETER_NAME((object-name), namespace-name) qualifier(tag-name))</tt> and <tt>BOOST_PARAMETER_NAME(qualifier(name))</tt>.  (Existing code that uses qualifiers directly and correctly with <tt>BOOST_PARAMETER_FUNCTION</tt> and other code generation macros should remain unaffected for now, so no breaking changes.)  The reason for the change in usage is to enable applying of parameter category constraints to Boost.Parameter-enabled functions and constructors invoked through argument composition.  (Otherwise, it is currently possible to use argument composition to bypass parameter category constraints applied in <tt>BOOST_PARAMETER_FUNCTION</tt> et. al.)</li>  
<li>Added support for perfect forwarding (<a href=“https://github.com/boostorg/parameter/pull/23”>PR#23</a>) (<a href=“https://github.com/boostorg/parameter/pull/26”>PR#26</a>), so that <tt>parameter::parameters::operator()</tt> can accept non-const rvalues.  As a positive side effect, Boost.Parameter-enabled functions and constructors are no longer bound by <tt>BOOST_PARAMETER_MAX_ARITY</tt> on compilers that support perfect forwarding.  User code can now check for this support by detecting the configuration macro <tt>BOOST_PARAMETER_HAS_PERFECT_FORWARDING</tt>, or manually turn off this support by defining the configuration macro <tt>BOOST_PARAMETER_DISABLE_PERFECT_FORWARDING</tt>.</li>  
<li>Added metafunctions <tt>boost::parameter::is_argument_pack</tt> (<a href=“https://github.com/boostorg/parameter/pull/27”>PR#27</a>), <tt>boost::parameter::are_tagged_arguments</tt> (<a href=“https://github.com/boostorg/parameter/pull/52”>PR#52</a>), and <tt>boost::parameter::result_of::compose</tt> (<a href=“https://github.com/boostorg/parameter/pull/75”>PR#75</a>).</li>  
<li>Added variadic function template <tt>boost::parameter::compose()</tt> which takes in named arguments and returns them in an argument pack (<a href=“https://github.com/boostorg/parameter/pull/52”>PR#52</a>).  For compilers that do not support perfect forwarding, the configuration macro <tt>BOOST_PARAMETER_COMPOSE_MAX_ARITY</tt> determines the maximum number of arguments that <tt>boost::parameter::compose()</tt> can take in (<a href=“https://github.com/boostorg/parameter/pull/61”>PR#61</a>).</li>  
<li>Added code generation macros <tt>BOOST_PARAMETER_BASIC_FUNCTION_CALL_OPERATOR</tt>, <tt>BOOST_PARAMETER_BASIC_CONST_FUNCTION_CALL_OPERATOR</tt>, <tt>BOOST_PARAMETER_NO_SPEC_FUNCTION</tt>, <tt>BOOST_PARAMETER_NO_SPEC_MEMBER_FUNCTION</tt>, <tt>BOOST_PARAMETER_NO_SPEC_CONST_MEMBER_FUNCTION</tt>, <tt>BOOST_PARAMETER_NO_SPEC_FUNCTION_CALL_OPERATOR</tt>, <tt>BOOST_PARAMETER_NO_SPEC_CONST_FUNCTION_CALL_OPERATOR</tt>, <tt>BOOST_PARAMETER_NO_SPEC_CONSTRUCTOR</tt>, and <tt>BOOST_PARAMETER_NO_SPEC_NO_BASE_CONSTRUCTOR</tt> (<a href=“https://github.com/boostorg/parameter/pull/52”>PR#52</a>).</li>  
<li>Added support for Boost.MP11 (<a href=“https://github.com/boostorg/parameter/pull/47”>PR#47</a>) (<a href=“https://github.com/boostorg/parameter/pull/66”>PR#66</a>) (<a href=“https://github.com/boostorg/parameter/pull/70”>PR#70</a>).  User code can now check for this support by detecting the configuration macro <tt>BOOST_PARAMETER_CAN_USE_MP11</tt>, or manually turn off this support by defining the configuration macro <tt>BOOST_PARAMETER_DISABLE_MP11_USAGE</tt>.</li>  
<li>Improved support for parameter-dependent return types via SFINAE (<a href=“https://github.com/boostorg/parameter/pull/73”>PR#73</a>).</li>  
</ul>

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2019-04-27 18:06:18 UTC  
> Url: https://github.com/boostorg/parameter/pull/77#issuecomment-487307670  

Looks like you've been hit by the Boost.Build changes mentioned on the list - don't ask me to explain it though!

---

## Comment 2

> Username: CromwellEnage  
> Created_at: 2019-04-27 18:40:22 UTC  
> Url: https://github.com/boostorg/parameter/pull/77#issuecomment-487309850  

I think I forgot to update the AppVeyor script by changing Visual Studio 2013 to Visual Studio 2015.  I'll see if doing so works.

---
