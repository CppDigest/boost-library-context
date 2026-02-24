# #446 X3: fix for attribute copying bug [Closed]

> Username: Xeverous  
> Created at: 2019-02-01 15:44:55 UTC  
> Updated at: 2019-02-03 17:28:40 UTC  
> Closed at: 2019-02-03 02:20:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/446  

X3 has used type transformations that unintentionally made copies  
of the suplied attribute. This prevented non-copyable AST types  
and had a potential performance impact.  
  
- `value_initialize` trait: removed `boost::value_initialized` dependency (which makes sense only for pre-11 standards), the code now uses C++11 brace initialization  
- `transform_attribute::pre` now uses `value_initialize` trait  
- removed all usages of (now redundant) `make_attribute` trait, its  
functionality is covered by `transform_attribute`  
- removed `make_attribute` and associated includes  
  
This fixes #444   
  
___  
  
question related to the code changes: At the end of `rule_parser::call_rule_definition` there is `traits::post_transform(attr, std::forward<transform_attr>(attr_));`. `transform_attr` can be a reference type - is then such forward intended? Shouldn't we `std::forward<std::remove_reference_t<transform_attr>>(attr_)` ?

---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2019-02-01 16:46:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/446#pullrequestreview-199158702  

Thanks, this is what I was thinking of. If it is possible please add tests for move only types and confirm that there are attribute synthesizing tests for actions and rules, I need it to safely merge the PR.

📁 include/boost/spirit/home/x3/nonterminal/detail/transform_attribute.hpp

```diff
  26 |+         static Transformed pre(Exposed&)
  27 |+         {
  28 |+             return traits::value_initialize<Transformed>::call();
```

> Username: Kojoley  
> Created_at: 2019-02-01 16:34:29 UTC  
> Updated_at: 2019-02-02 18:16:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/446#discussion_r253115101  

Hm, I thought `make_attribute` uses `value_initialize`, maybe we do not need it at all. Revert this for now.

> Username: Kojoley  
> Created_at: 2019-02-01 18:17:03 UTC  
> Updated_at: 2019-02-02 18:16:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/446#discussion_r253148390  

Qi's `make_attribute` uses `value_initialize`, that's where my confusion came from. Currently I do not know why Qi uses it (from `value_initialized` documentation it looks like it used wrongly), and Qi does not have `value_initialize` so no need to add complexity until there is a real need for it.


📁 include/boost/spirit/home/x3/support/traits/value_traits.hpp

```diff
  18 |         {
  21 |-             return boost::value_initialized<T>();
  19 |+             return {};
```

> Username: Kojoley  
> Created_at: 2019-02-01 16:37:00 UTC  
> Updated_at: 2019-02-02 18:16:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/446#discussion_r253115993  

Please file this as a separate PR. The change does not need addition tests, while other changes in this PR do.


---

## Comment 2

> Username: Xeverous  
> Created_at: 2019-02-01 16:58:57 UTC  
> Updated_at: 2019-02-01 17:11:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/446#issuecomment-459791457  

@Kojoley tests: how would you like tests to be done? I see 2 options:  
  
- use the minimal working example from referenced issue, this will cause test compilation to fail in case of regression  
- write the class so that the copying is possible but the class notes that it has been copied (more complex code (need to avoid copy elision) but test will pass/fail instead)  
  
Also, which/what attribute synthesizing you mean exactly? I'm not sure what to look in tests for.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2019-02-01 17:41:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/446#issuecomment-459805140  

Compile tests are preferred. You can use some known move only type like `unique_ptr` if it is easier for you (we do not need to proof that `unique_ptr` is move only). There are attribute synthesizing tests in `rule3.cpp`, it is ok to tune them to use move only type.  
  
Currently the test suit fails on your PR, start by fixing them.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2019-02-01 17:54:23 UTC  
> Url: https://github.com/boostorg/spirit/pull/446#issuecomment-459809146  

> question related to the code changes: At the end of rule_parser::call_rule_definition   
> there is traits::post_transform(attr, std::forward<transform_attr>(attr_));. transform_attr can be a reference type - is then such forward intended? Shouldn't we std::forward<std::remove_reference_t<transform_attr>>(attr_) ?  
  
No, it is fine as is. The forward returns lvalue if template parameter was lvalue and rvalue if template parameter was rvalue reference or value type - that's exactly what we want to pass to `post`.

---

## Comment 5

> Username: Xeverous  
> Created_at: 2019-02-02 16:04:58 UTC  
> Updated_at: 2019-02-02 16:16:37 UTC  
> Url: https://github.com/boostorg/spirit/pull/446#issuecomment-459976549  

What is the point of these specializations?  
  
```cpp  
    template <typename Attribute>  
    struct transform_attribute<unused_type, Attribute>  
      : transform_attribute<unused_type, unused_type> {};  
  
    template <typename Attribute>  
    struct transform_attribute<unused_type const, Attribute>  
      : transform_attribute<unused_type, unused_type> {};  
```  
  
Both end making `unused_type` when the `Transformed` was supposed to be something that is not unused. They cause current patch to fail the build, because in the `parse()` implementation `unused_type` is returned from `_attr` which is obviously wrong.  
  
I don't get these specializations. If I understand correctly what we want to do (assuming combined `make_attribute` and `transform_attribute` implementation) is:  
  
default: `type = Transformed`  
  
- spec when `Exposed == Transformed`: `type = Transformed&`  
- spec when `Exposed == unused_type`: `type = Transformed`  
- spec when `Transformed = unused_type`: `type = unused_type`  
  
Can you explain the full purpose of the `transform_attribute` trait? `make_attribute` was simply to create an object of wanted attribute type, but I don't get why `transform_attribute` has `Transformed == unused_type` when the original attribute was something.  
  
If we want to combine their implementation to 1 trait, we need to make some changes in the `transform_attribute` implementation.

---

## Comment 6

> Username: Kojoley  
> Created_at: 2019-02-02 16:24:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/446#issuecomment-459978080  

That's an open question. Remember that `transform_attribute` was used with `make_attribute` so these specialization seems to be never used and possibly are wrong. I am pretty sure the code was borrowed from Qi, I have tracked it down to 4004c77a49710fe9bc3e2a70bbff73abbc994376 commit that does not answer the question, I do not think that @hkaiser remember the details (it was 10 years ago). Also there are `const` specialization (I think it is a Karma specific things that leaked into Qi and X3) and reference specializations that possibly another unneeded thing.

---

## Review 7 [Commented]

> Username: Kojoley  
> Created_at: 2019-02-02 16:33:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/446#pullrequestreview-199353550  

📁 include/boost/spirit/home/x3/core/action.hpp

```diff
  91 |-             typename make_attribute::type made_attr = make_attribute::call(unused_type());
  92 |-             typename transform::type attr = transform::pre(made_attr);
  89 |+             typename transform::type attr = transform::pre(unused_type());
```

> Username: Kojoley  
> Created_at: 2019-02-02 16:33:46 UTC  
> Updated_at: 2019-02-02 18:16:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/446#discussion_r253270630  

I think we can replace this with `attribute_type attr{};`. No one should specialize something like `transform_attribute<unused_type, int>`, it would be nonsense.


---

## Comment 8

> Username: Xeverous  
> Created_at: 2019-02-02 16:53:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/446#issuecomment-459980220  

ok, so we can start implementing the class from scratch - it is not used anywhere else in the library besides action and rule  
  
assuming the task is to just provide any meaningful attribute for the parsing, the implementation can go like this:  
  
- Exposed: the type that user provided (may be unused)  
- Transformed: the desired type that is needed for parsing  
  
default case:  
- `type = Transformed`  
- `pre(Exposed&)` returns value-constructed object of type `Transformed`  
- `post(Exposed&, Transformed&&)` moves from `Transformed` to `Exposed`  
  
case when `Exposed == Transformed` (optimize by using references)  
- `type = Transformed&`  
- `pre(Exposed&)`: return reference to the provided object  
- `post()`: no-op  
  
case when `Exposed = unused_type`  
- `type = Transformed`  
- `pre(unused_type)` returns value-constructed object of type `Transformed`  
- `post(unused_type, const Transformed&)`: no-op  
  
other cases (reference ones and twice unused) - I don't see the point right now  
  
What do you think?

---

## Comment 9

> Username: Kojoley  
> Created_at: 2019-02-02 17:09:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/446#issuecomment-459981505  

> ok, so we can start implementing the class from scratch - it is not used anywhere else in the library besides action and rule  
  
The `transform_attribute` trait is a customization point for users, so if you make something backward incompatible with it potentially a lot of user code will stop working and that is usually not acceptable without a deprecation period. Technically anything that is not under `detail` namespace is public API and should be modified with caution (that's why removing `make_attribute` is also a breaking change, but because it did not provide anything useful and `transform_attribute` does a job for it I think we are safe to remove it).  
  
I do not see why it is needed to be remade from scratch. Is not removing those offending specializations solve the CI failures?

---

## Comment 10

> Username: Kojoley  
> Created_at: 2019-02-02 17:27:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/446#issuecomment-459982862  

Are you sure that offending specialization not these? https://github.com/boostorg/spirit/blob/dc440dd4947ae36392b940cddc85cf8e5b70dffc/include/boost/spirit/home/x3/nonterminal/detail/transform_attribute.hpp#L81-L87  
  
Or I will look myself later today.

---

## Comment 11

> Username: Xeverous  
> Created_at: 2019-02-02 17:27:27 UTC  
> Updated_at: 2019-02-02 17:30:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/446#issuecomment-459982874  

> Technically anything that is not under detail namespace is public API and should be modified with caution  
  
By rewriting from scratch I did not mean I wanted to make the class very different. I meant that we can write the most minimal working code that satisfied our needs - in others words, don't copy any code from previous implementation just beause it was there - find a reason first.  
  
Also, do you have any data how big part of users use the full non-detail API? For me the public API is only the stuff that is in the documentation because anything else requires to browse library code. X3 really needs a larger tutorial, I bet there are users (including me) which don't use significant parts of X3 public API just because they are not aware of their existence.  
  
>  Is not removing those offending specializations solve the CI failures?  
  
Not really - there is a small issue - default implementation (which after removal of offending specializations also is used in the case of `<unused_type, Attribute>`) takes argument by non-const lvalue ref, which errors upon `transform::pre(unused_type());`.  
  
To compile action test after removal of offending specializations, I had to specialize for the case of `<unused_type, Attribute>` so that we can take `unused_type` by value and have no-op `post()`:  
  
```cpp  
    template <typename Attribute>  
    struct transform_attribute<unused_type, Attribute>  
    {  
	    typedef Attribute type;  
	    static type pre(unused_type) { return type(); }  
	    static void post(unused_type, const Attribute&) {}  
    };  
  
    template <typename Attribute>  
    struct transform_attribute<unused_type const, Attribute>  
      : transform_attribute<unused_type, Attribute> {};  
```  
  
I can push this and we will see whether all tests succeed.

---

## Review 12 [Commented]

> Username: Xeverous  
> Created_at: 2019-02-02 18:12:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/446#pullrequestreview-199356911  

📁 include/boost/spirit/home/x3/nonterminal/detail/transform_attribute.hpp

```diff
  11 | #include <boost/spirit/home/x3/support/traits/transform_attribute.hpp>
  12 | #include <boost/spirit/home/x3/support/traits/move_to.hpp>
  13 |+ #include <boost/spirit/home/x3/support/traits/value_traits.hpp>
```

> Username: Xeverous  
> Created_at: 2019-02-02 18:12:53 UTC  
> Updated_at: 2019-02-02 18:16:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/446#discussion_r253273480  

accidental stray edit, remove this when adding tests

> Username: cppljevans  
> Created_at: 2019-02-02 19:39:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/446#discussion_r253276256  

I'm getting error when compiling test/char_class.cpp:  
```bash  
xeverous/spirit/test/x3/char_class.cpp:110:26: warning: illegal character encoding in string literal [-Winvalid-source-encoding]  
        BOOST_TEST(test("<E9>", alpha));  
                         ^~~~  
```  
Is that expected?

> Username: Kojoley  
> Created_at: 2019-02-02 19:54:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/446#discussion_r253276648  

Yes, read a header comment in `char_class.cpp`.


---

## Comment 13

> Username: Kojoley  
> Created_at: 2019-02-02 20:08:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/446#issuecomment-459995359  

FYI I opened PR #448 that removes redundant and wrong specializations.

---

## Comment 14

> Username: Kojoley  
> Created_at: 2019-02-02 20:52:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/446#issuecomment-459998251  

It seems that the things are more complicated and there was a hidden bug.  
  
```cpp  
#include <boost/core/lightweight_test.hpp>  
#include <boost/spirit/home/x3.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
  
struct Foo  
{  
    Foo() : i{}, j{} { ++constructed; }  
  
    static int constructed;  
    int i, j;  
};  
  
int Foo::constructed = 0;  
  
BOOST_FUSION_ADAPT_STRUCT(Foo, i, j)  
  
  
int main()  
{  
    {  
        using boost::spirit::x3::int_;  
        using boost::spirit::x3::rule;  
  
        auto p = rule<class r, Foo>{} = '{' >> int_ >> ':' >> int_ >> '}';  
  
        auto s = "{123:456}", e = s + std::strlen(s);  
        BOOST_TEST(parse(s, e, p));  
        BOOST_TEST_EQ(Foo::constructed, 0);  
    }  
  
    return boost::report_errors();  
}  
```  
  
https://wandbox.org/permlink/Iznpoky24gcTeL2H  
  
`make_attribute` synthesizes the attribute when it is not needed, and then `transform_attribute` does not let it pass to the parser, but with your last change (and in #448) it now propagates to the parser (what will case performance degradation).

---

## Comment 15

> Username: Kojoley  
> Created_at: 2019-02-03 02:20:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/446#issuecomment-460016910  

I have a series of PR that will fix you problem. The first is #449.

---

## Comment 16

> Username: Xeverous  
> Created_at: 2019-02-03 17:20:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/446#issuecomment-460070338  

What do you think about adding or modifying existing test to make one with non-copyable AST?

---

## Comment 17

> Username: Kojoley  
> Created_at: 2019-02-03 17:28:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/446#issuecomment-460071063  

I will add this test https://github.com/Kojoley/spirit/commit/00ed9a2b69742c69394e54366ea27c1c37db08d5 after #449 is merged.

---
