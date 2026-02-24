# #484 - BOOST_HANA_ADAPT_STRUCT for templates? [Open]

> Username: arximboldi  
> Created at: 2021-03-02 13:42:44 UTC  
> Updated at: 2021-04-09 17:21:11 UTC  
> Url: https://github.com/boostorg/hana/issues/484  

I would love to be able to adapt structs like this:  
  
```  
template <typename T>  
struct foo {  
  T member;  
}  
  
BOOST_HANA_ADAPT_STRUCT_TPL(T, foo, member);  
```  
  
Looking at the `accessors_impl` interface, it feels like something doable...

---

## Comment 1

> Username: haras-unicorn  
> Created at: 2021-04-09 17:08:30 UTC  
> Updated at: 2021-04-09 17:21:11 UTC  
> Url: https://github.com/boostorg/hana/issues/484#issuecomment-816825981  

I was looking into this as well and got it working using a hack:  
```  
namespace test  
{  
template<typename T>  
struct vector  
{  
    BOOST_HANA_DEFINE_STRUCT(vector, (T, x), (T, y));  
};  
  
struct vector_tag  
{  
    using sample = vector<int>;  
};  
} // namespace test  
  
namespace boost::hana  
{  
template<typename TTag>  
struct accessors_impl<TTag, when<struct_detail::is_valid<typename TTag::sample>::value>>  
{  
private:  
    static constexpr auto sample_inner =  
            TTag::sample::hana_accessors_impl::apply();  
  
    template<typename T>  
    static constexpr decltype(T::hana_accessors_impl::apply()) inner =  
            T::hana_accessors_impl::apply();  
  
    template<std::size_t... I, typename... T>  
    static constexpr decltype(auto) _apply(  
            detail::basic_tuple_impl<std::index_sequence<I...>, T...>  
                    _sample_inner)  
    {  
        constexpr auto getter =  
                [](auto i, auto&& x) {  
                    using type = decltype(x);  
                    using unqualified =  
                            std::remove_reference_t<  
                                    std::remove_cv_t<type>>;  
  
                    return second(inner<unqualified>[i])(  
                            std::forward<type>(x));  
                };  
  
        return make_tuple(  
                make_pair(  
                        first(detail::ebo_get<detail::bti<I>>(_sample_inner)),  
                        partial(getter, size_c<I>))...);  
    }  
  
public:  
    static constexpr decltype(auto) apply()  
    {  
        return _apply(sample_inner.storage_);  
    }  
};  
} // namespace boost::hana  
```  
  
Here's a test I used using GoogleTest to confirm its working:  
```  
namespace test  
{  
constexpr auto vector_accessors = ::boost::hana::accessors<vector_tag>();  
} // namespace test  
  
ENV_TEST(env, regular)  
{  
    ::test::vector<int> a{1, 2};  
    ::test::vector<int> b{2, 3};  
    EXPECT_PRED2(::boost::hana::not_equal, a, b);  
    EXPECT_PRED2(::boost::hana::in, BOOST_HANA_STRING("x"), a);  
    EXPECT_EQ(  
            ::boost::hana::keys(a),  
            ::boost::hana::make_tuple(  
                    BOOST_HANA_STRING("x"),  
                    BOOST_HANA_STRING("y")));  
    EXPECT_EQ(::boost::hana::members(a),  
              ::boost::hana::make_tuple(  
                      1, 2));  
  
    constexpr auto get_vector_x =  
            ::boost::hana::second(test::vector_accessors[0_c]);  
    EXPECT_EQ(get_vector_x(a), 1);  
}  
```  
  
You just need to define a tag with a sample specialization.  
It needs more testing and it would be nice if the code was prettier, but it works for now.
