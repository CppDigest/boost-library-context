# #994 - Compilation failed: conflicting declaration of `boost_concept_check93` [Open]

> Username: edisonhello  
> Created at: 2024-12-24 07:05:02 UTC  
> Updated at: 2024-12-24 16:31:07 UTC  
> Url: https://github.com/boostorg/boost/issues/994  

Full compilation error:  
```  
In file included from /home/edison/Downloads/boost_1_87_0/boost/config.hpp:39,  
                 from /home/edison/Downloads/boost_1_87_0/boost/range/iterator_range_core.hpp:17,  
                 from ce.cpp:109:  
/home/edison/Downloads/boost_1_87_0/boost/config/compiler/gcc.hpp:341:60: error: redeclaration of ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost_concepts::SinglePassIterator<ConstIterator>)>::failed)> boost_concepts::InteroperableIterator<Iterator, ConstIterator>::boost_concept_check93’  
  341 | #  define BOOST_ATTRIBUTE_UNUSED __attribute__((__unused__))  
      |                                                            ^  
In file included from /home/edison/Downloads/boost_1_87_0/boost/concept/assert.hpp:35,  
                 from /home/edison/Downloads/boost_1_87_0/boost/concept_check.hpp:20,  
                 from /home/edison/Downloads/boost_1_87_0/boost/range/concepts.hpp:19,  
                 from /home/edison/Downloads/boost_1_87_0/boost/range/size_type.hpp:20,  
                 from /home/edison/Downloads/boost_1_87_0/boost/range/size.hpp:21,  
                 from /home/edison/Downloads/boost_1_87_0/boost/range/functions.hpp:20,  
                 from /home/edison/Downloads/boost_1_87_0/boost/range/iterator_range_core.hpp:38:  
/home/edison/Downloads/boost_1_87_0/boost/concept/detail/general.hpp:93:20: note: previous declaration ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost_concepts::SinglePassIterator<Iterator>)>::failed)> boost_concepts::InteroperableIterator<Iterator, ConstIterator>::boost_concept_check93’  
   93 |       BOOST_PP_CAT(boost_concept_check,__LINE__)             \  
      |                    ^~~~~~~~~~~~~~~~~~~  
/home/edison/Downloads/boost_1_87_0/boost/config/compiler/gcc.hpp:341:60: error: redeclaration of ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::concepts::usage_requirements<boost_concepts::InteroperableIterator<Iterator, ConstIterator> >)>::failed)> boost_concepts::InteroperableIterator<Iterator, ConstIterator>::boost_concept_check93’  
  341 | #  define BOOST_ATTRIBUTE_UNUSED __attribute__((__unused__))  
      |                                                            ^  
/home/edison/Downloads/boost_1_87_0/boost/concept/detail/general.hpp:93:20: note: previous declaration ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost_concepts::SinglePassIterator<Iterator>)>::failed)> boost_concepts::InteroperableIterator<Iterator, ConstIterator>::boost_concept_check93’  
   93 |       BOOST_PP_CAT(boost_concept_check,__LINE__)             \  
      |                    ^~~~~~~~~~~~~~~~~~~  
/home/edison/Downloads/boost_1_87_0/boost/config/compiler/gcc.hpp:341:60: error: redeclaration of ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::concepts::usage_requirements<boost::range_detail::IncrementableIteratorConcept<Iterator> >)>::failed)> boost::range_detail::IncrementableIteratorConcept<Iterator>::boost_concept_check93’  
  341 | #  define BOOST_ATTRIBUTE_UNUSED __attribute__((__unused__))  
      |                                                            ^  
/home/edison/Downloads/boost_1_87_0/boost/concept/detail/general.hpp:93:20: note: previous declaration ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::Convertible<typename boost::iterators::iterator_traversal<Iterator>::type, boost::iterators::incrementable_traversal_tag>)>::failed)> boost::range_detail::IncrementableIteratorConcept<Iterator>::boost_concept_check93’  
   93 |       BOOST_PP_CAT(boost_concept_check,__LINE__)             \  
      |                    ^~~~~~~~~~~~~~~~~~~  
/home/edison/Downloads/boost_1_87_0/boost/config/compiler/gcc.hpp:341:60: error: redeclaration of ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::concepts::usage_requirements<boost::range_detail::SinglePassIteratorConcept<Iterator> >)>::failed)> boost::range_detail::SinglePassIteratorConcept<Iterator>::boost_concept_check93’  
  341 | #  define BOOST_ATTRIBUTE_UNUSED __attribute__((__unused__))  
      |                                                            ^  
/home/edison/Downloads/boost_1_87_0/boost/concept/detail/general.hpp:93:20: note: previous declaration ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::Convertible<typename boost::range_detail::SinglePassIteratorConcept<Iterator>::traversal_category, boost::iterators::single_pass_traversal_tag>)>::failed)> boost::range_detail::SinglePassIteratorConcept<Iterator>::boost_concept_check93’  
   93 |       BOOST_PP_CAT(boost_concept_check,__LINE__)             \  
      |                    ^~~~~~~~~~~~~~~~~~~  
In file included from /home/edison/Downloads/boost_1_87_0/boost/mpl/aux_/na_assert.hpp:23,  
                 from /home/edison/Downloads/boost_1_87_0/boost/mpl/arg.hpp:25,  
                 from /home/edison/Downloads/boost_1_87_0/boost/mpl/placeholders.hpp:24,  
                 from /home/edison/Downloads/boost_1_87_0/boost/iterator/iterator_categories.hpp:16,  
                 from /home/edison/Downloads/boost_1_87_0/boost/iterator/iterator_facade.hpp:13,  
                 from /home/edison/Downloads/boost_1_87_0/boost/range/iterator_range_core.hpp:27:  
/home/edison/Downloads/boost_1_87_0/boost/mpl/assert.hpp:352:9: error: ‘mpl_assertion_in_line_22’ conflicts with a previous declaration  
  352 |         ) \  
      |         ^  
/home/edison/Downloads/boost_1_87_0/boost/mpl/assert.hpp:298:20: note: previous declaration ‘boost::range_detail::ForwardIteratorConcept<Iterator>::<unnamed enum> boost::range_detail::ForwardIteratorConcept<Iterator>::mpl_assertion_in_line_22’  
  298 |     , BOOST_PP_CAT(mpl_assertion_in_line_,BOOST_MPL_AUX_PP_COUNTER()) = sizeof( \  
      |                    ^~~~~~~~~~~~~~~~~~~~~~  
/home/edison/Downloads/boost_1_87_0/boost/config/compiler/gcc.hpp:341:60: error: redeclaration of ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::concepts::usage_requirements<boost::range_detail::ForwardIteratorConcept<Iterator> >)>::failed)> boost::range_detail::ForwardIteratorConcept<Iterator>::boost_concept_check93’  
  341 | #  define BOOST_ATTRIBUTE_UNUSED __attribute__((__unused__))  
      |                                                            ^  
/home/edison/Downloads/boost_1_87_0/boost/concept/detail/general.hpp:93:20: note: previous declaration ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::Convertible<typename boost::range_detail::ForwardIteratorConcept<Iterator>::traversal_category, boost::iterators::forward_traversal_tag>)>::failed)> boost::range_detail::ForwardIteratorConcept<Iterator>::boost_concept_check93’  
   93 |       BOOST_PP_CAT(boost_concept_check,__LINE__)             \  
      |                    ^~~~~~~~~~~~~~~~~~~  
/home/edison/Downloads/boost_1_87_0/boost/config/compiler/gcc.hpp:341:60: error: redeclaration of ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::concepts::usage_requirements<boost::range_detail::BidirectionalIteratorConcept<Iterator> >)>::failed)> boost::range_detail::BidirectionalIteratorConcept<Iterator>::boost_concept_check93’  
  341 | #  define BOOST_ATTRIBUTE_UNUSED __attribute__((__unused__))  
      |                                                            ^  
/home/edison/Downloads/boost_1_87_0/boost/concept/detail/general.hpp:93:20: note: previous declaration ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::Convertible<typename boost::range_detail::BidirectionalIteratorConcept<Iterator>::traversal_category, boost::iterators::bidirectional_traversal_tag>)>::failed)> boost::range_detail::BidirectionalIteratorConcept<Iterator>::boost_concept_check93’  
   93 |       BOOST_PP_CAT(boost_concept_check,__LINE__)             \  
      |                    ^~~~~~~~~~~~~~~~~~~  
/home/edison/Downloads/boost_1_87_0/boost/config/compiler/gcc.hpp:341:60: error: redeclaration of ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::concepts::usage_requirements<boost::range_detail::RandomAccessIteratorConcept<Iterator> >)>::failed)> boost::range_detail::RandomAccessIteratorConcept<Iterator>::boost_concept_check93’  
  341 | #  define BOOST_ATTRIBUTE_UNUSED __attribute__((__unused__))  
      |                                                            ^  
/home/edison/Downloads/boost_1_87_0/boost/concept/detail/general.hpp:93:20: note: previous declaration ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::Convertible<typename boost::range_detail::RandomAccessIteratorConcept<Iterator>::traversal_category, boost::iterators::random_access_traversal_tag>)>::failed)> boost::range_detail::RandomAccessIteratorConcept<Iterator>::boost_concept_check93’  
   93 |       BOOST_PP_CAT(boost_concept_check,__LINE__)             \  
      |                    ^~~~~~~~~~~~~~~~~~~  
/home/edison/Downloads/boost_1_87_0/boost/config/compiler/gcc.hpp:341:60: error: redeclaration of ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::range_detail::SinglePassIteratorConcept<typename boost::range_iterator<const typename boost::remove_reference<T>::type>::type>)>::failed)> boost::SinglePassRangeConcept<T>::boost_concept_check93’  
  341 | #  define BOOST_ATTRIBUTE_UNUSED __attribute__((__unused__))  
      |                                                            ^  
/home/edison/Downloads/boost_1_87_0/boost/concept/detail/general.hpp:93:20: note: previous declaration ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::range_detail::SinglePassIteratorConcept<typename boost::range_iterator<typename boost::remove_reference<T>::type, void>::type>)>::failed)> boost::SinglePassRangeConcept<T>::boost_concept_check93’  
   93 |       BOOST_PP_CAT(boost_concept_check,__LINE__)             \  
      |                    ^~~~~~~~~~~~~~~~~~~  
/home/edison/Downloads/boost_1_87_0/boost/config/compiler/gcc.hpp:341:60: error: redeclaration of ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::concepts::usage_requirements<boost::SinglePassRangeConcept<T> >)>::failed)> boost::SinglePassRangeConcept<T>::boost_concept_check93’  
  341 | #  define BOOST_ATTRIBUTE_UNUSED __attribute__((__unused__))  
      |                                                            ^  
/home/edison/Downloads/boost_1_87_0/boost/concept/detail/general.hpp:93:20: note: previous declaration ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::range_detail::SinglePassIteratorConcept<typename boost::range_iterator<typename boost::remove_reference<T>::type, void>::type>)>::failed)> boost::SinglePassRangeConcept<T>::boost_concept_check93’  
   93 |       BOOST_PP_CAT(boost_concept_check,__LINE__)             \  
      |                    ^~~~~~~~~~~~~~~~~~~  
/home/edison/Downloads/boost_1_87_0/boost/config/compiler/gcc.hpp:341:60: error: redeclaration of ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::range_detail::ForwardIteratorConcept<typename boost::ForwardRangeConcept<T>::const_iterator>)>::failed)> boost::ForwardRangeConcept<T>::boost_concept_check93’  
  341 | #  define BOOST_ATTRIBUTE_UNUSED __attribute__((__unused__))  
      |                                                            ^  
/home/edison/Downloads/boost_1_87_0/boost/concept/detail/general.hpp:93:20: note: previous declaration ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::range_detail::ForwardIteratorConcept<typename boost::ForwardRangeConcept<T>::iterator>)>::failed)> boost::ForwardRangeConcept<T>::boost_concept_check93’  
   93 |       BOOST_PP_CAT(boost_concept_check,__LINE__)             \  
      |                    ^~~~~~~~~~~~~~~~~~~  
/home/edison/Downloads/boost_1_87_0/boost/config/compiler/gcc.hpp:341:60: error: redeclaration of ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::range_detail::BidirectionalIteratorConcept<typename boost::BidirectionalRangeConcept<T>::const_iterator>)>::failed)> boost::BidirectionalRangeConcept<T>::boost_concept_check93’  
  341 | #  define BOOST_ATTRIBUTE_UNUSED __attribute__((__unused__))  
      |                                                            ^  
/home/edison/Downloads/boost_1_87_0/boost/concept/detail/general.hpp:93:20: note: previous declaration ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::range_detail::BidirectionalIteratorConcept<typename boost::BidirectionalRangeConcept<T>::iterator>)>::failed)> boost::BidirectionalRangeConcept<T>::boost_concept_check93’  
   93 |       BOOST_PP_CAT(boost_concept_check,__LINE__)             \  
      |                    ^~~~~~~~~~~~~~~~~~~  
/home/edison/Downloads/boost_1_87_0/boost/config/compiler/gcc.hpp:341:60: error: redeclaration of ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::range_detail::RandomAccessIteratorConcept<typename boost::RandomAccessRangeConcept<T>::const_iterator>)>::failed)> boost::RandomAccessRangeConcept<T>::boost_concept_check93’  
  341 | #  define BOOST_ATTRIBUTE_UNUSED __attribute__((__unused__))  
      |                                                            ^  
/home/edison/Downloads/boost_1_87_0/boost/concept/detail/general.hpp:93:20: note: previous declaration ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::range_detail::RandomAccessIteratorConcept<typename boost::RandomAccessRangeConcept<T>::iterator>)>::failed)> boost::RandomAccessRangeConcept<T>::boost_concept_check93’  
   93 |       BOOST_PP_CAT(boost_concept_check,__LINE__)             \  
      |                    ^~~~~~~~~~~~~~~~~~~  
/home/edison/Downloads/boost_1_87_0/boost/concept/detail/general.hpp: In function ‘bool boost::range::equal(const SinglePassRange1&, const SinglePassRange2&)’:  
/home/edison/Downloads/boost_1_87_0/boost/concept/detail/general.hpp:93:20: error: conflicting declaration ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::SinglePassRangeConcept<const SinglePassRange2>)>::failed)> boost_concept_check93’  
/home/edison/Downloads/boost_1_87_0/boost/concept/detail/general.hpp:93:20: note: previous declaration as ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::SinglePassRangeConcept<const SinglePassRange1>)>::failed)> boost_concept_check93’  
/home/edison/Downloads/boost_1_87_0/boost/concept/detail/general.hpp: In function ‘bool boost::range::equal(const SinglePassRange1&, const SinglePassRange2&, BinaryPredicate)’:  
/home/edison/Downloads/boost_1_87_0/boost/concept/detail/general.hpp:93:20: error: conflicting declaration ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::SinglePassRangeConcept<const SinglePassRange2>)>::failed)> boost_concept_check93’  
/home/edison/Downloads/boost_1_87_0/boost/concept/detail/general.hpp:93:20: note: previous declaration as ‘typedef struct boost::concepts::detail::instantiate<(& boost::concepts::requirement_<void (*)(boost::SinglePassRangeConcept<const SinglePassRange1>)>::failed)> boost_concept_check93’  
```  
  
Source code (and also, this is the minimal reproducible example I can get):  
```  
#include <span>  
  
// Reference: https://www.scs.stanford.edu/~dm/blog/va-opt.html  
#define PARENS ()  
#define EXPAND(...) EXPAND6(EXPAND6(EXPAND6(EXPAND6(__VA_ARGS__))))  
#define EXPAND6(...) EXPAND5(EXPAND5(EXPAND5(EXPAND5(__VA_ARGS__))))  
#define EXPAND5(...) EXPAND4(EXPAND4(EXPAND4(EXPAND4(__VA_ARGS__))))  
#define EXPAND4(...) EXPAND3(EXPAND3(EXPAND3(EXPAND3(__VA_ARGS__))))  
#define EXPAND3(...) EXPAND2(EXPAND2(EXPAND2(EXPAND2(__VA_ARGS__))))  
#define EXPAND2(...) EXPAND1(EXPAND1(EXPAND1(EXPAND1(__VA_ARGS__))))  
#define EXPAND1(...) __VA_ARGS__  
  
#define FOR_EACH(macro, ...) \  
    __VA_OPT__(EXPAND(FOR_EACH_HELPER(macro, __VA_ARGS__)))  
#define FOR_EACH_HELPER(macro, a1, ...) \  
    macro(a1) __VA_OPT__(FOR_EACH_AGAIN PARENS(macro, __VA_ARGS__))  
#define FOR_EACH_AGAIN() FOR_EACH_HELPER  
  
#define FAKE_HEADER(name)                            \  
    namespace ns::name {                             \  
    class Class : public IClass {                    \  
       public:                                       \  
        Class();                                     \  
        static size_t Sizeof();                      \  
        size_t Size() const override;                \  
        void F2(const int &, int) override;          \  
        void F3(const int &, int) override;          \  
        void F4(const int &, int) override;          \  
        void F5(const int &, int) override;          \  
        void F6() override;                          \  
        void F7(std::span<double> v) const override; \  
    };                                               \  
    }  
  
class IClass {  
   public:  
    IClass() = default;  
  
    virtual ~IClass() = default;  
  
    virtual size_t Size() const = 0;  
  
    virtual void F2(const int &, int);  
  
    virtual void F3(const int &, int);  
  
    virtual void F4(const int &, int);  
  
    virtual void F5(const int &, int);  
  
    virtual void F6();  
  
    virtual void F7(std::span<double> v) const = 0;  
};  
  
#define LIST                                                                  \  
    cl0, cl1, cl2, cl3, cl4, cl5, cl6, cl7, cl8, cl9, cl10, cl11, cl12, cl13, \  
        cl14, cl15, cl16, cl17, cl18, cl19, cl20, cl21, cl22, cl23, cl24,     \  
        cl25, cl26, cl27, cl28, cl29, cl30, cl31, cl32, cl33, cl34, cl35,     \  
        cl36, cl37, cl38, cl39, cl40, cl41, cl42, cl43, cl44, cl45, cl46,     \  
        cl47, cl48, cl49, cl50, cl51, cl52, cl53, cl54, cl55, cl56, cl57,     \  
        cl58, cl59, cl60, cl61, cl62, cl63, cl64, cl65, cl66, cl67, cl68,     \  
        cl69, cl70, cl71, cl72, cl73, cl74, cl75, cl76, cl77, cl78, cl79,     \  
        cl80, cl81, cl82, cl83, cl84, cl85, cl86, cl87, cl88, cl89, cl90,     \  
        cl91, cl92, cl93, cl94, cl95, cl96, cl97, cl98, cl99, cl100, cl101,   \  
        cl102, cl103, cl104, cl105, cl106, cl107, cl108, cl109, cl110, cl111, \  
        cl112, cl113, cl114, cl115, cl116, cl117, cl118, cl119, cl120, cl121, \  
        cl122, cl123, cl124, cl125, cl126, cl127, cl128, cl129, cl130, cl131, \  
        cl132, cl133, cl134, cl135, cl136, cl137, cl138, cl139, cl140, cl141, \  
        cl142, cl143, cl144, cl145, cl146, cl147, cl148, cl149, cl150, cl151, \  
        cl152, cl153, cl154, cl155, cl156, cl157, cl158, cl159, cl160, cl161, \  
        cl162, cl163, cl164, cl165, cl166, cl167, cl168, cl169, cl170, cl171, \  
        cl172, cl173, cl174, cl175, cl176, cl177, cl178, cl179, cl180, cl181, \  
        cl182, cl183, cl184, cl185, cl186, cl187, cl188, cl189, cl190, cl191, \  
        cl192, cl193, cl194, cl195, cl196, cl197, cl198, cl199, cl200, cl201, \  
        cl202, cl203, cl204, cl205, cl206, cl207, cl208, cl209, cl210, cl211, \  
        cl212, cl213, cl214, cl215, cl216, cl217, cl218, cl219, cl220, cl221, \  
        cl222, cl223, cl224, cl225, cl226, cl227, cl228, cl229, cl230, cl231, \  
        cl232, cl233, cl234, cl235, cl236, cl237, cl238, cl239, cl240, cl241, \  
        cl242, cl243, cl244, cl245, cl246, cl247, cl248, cl249, cl250, cl251, \  
        cl252, cl253, cl254, cl255, cl256, cl257, cl258, cl259, cl260, cl261, \  
        cl262, cl263, cl264, cl265, cl266, cl267, cl268, cl269, cl270, cl271, \  
        cl272, cl273, cl274, cl275, cl276, cl277, cl278, cl279, cl280, cl281, \  
        cl282, cl283, cl284, cl285, cl286, cl287, cl288, cl289, cl290, cl291, \  
        cl292, cl293, cl294, cl295, cl296, cl297, cl298, cl299, cl300, cl301, \  
        cl302, cl303, cl304, cl305, cl306, cl307, cl308, cl309, cl310, cl311, \  
        cl312, cl313, cl314, cl315, cl316, cl317, cl318, cl319, cl320, cl321, \  
        cl322, cl323, cl324, cl325, cl326, cl327, cl328, cl329, cl330, cl331, \  
        cl332, cl333, cl334, cl335, cl336, cl337, cl338, cl339, cl340, cl341, \  
        cl342, cl343, cl344, cl345, cl346, cl347, cl348, cl349, cl350, cl351, \  
        cl352, cl353, cl354, cl355, cl356, cl357, cl358, cl359, cl360, cl361, \  
        cl362, cl363, cl364, cl365, cl366, cl367, cl368, cl369, cl370, cl371, \  
        cl372, cl373, cl374, cl375, cl376, cl377, cl378, cl379, cl380, cl381, \  
        cl382, cl383, cl384, cl385, cl386, cl387, cl388, cl389, cl390, cl391, \  
        cl392, cl393, cl394, cl395, cl396, cl397, cl398, cl399, cl400, cl401, \  
        cl402, cl403, cl404, cl405, cl406, cl407, cl408, cl409, cl410, cl411, \  
        cl412, cl413, cl414, cl415, cl416, cl417, cl418, cl419, cl420, cl421, \  
        cl422, cl423, cl424, cl425, cl426, cl427, cl428, cl429, cl430, cl431, \  
        cl432, cl433, cl434, cl435, cl436, cl437, cl438, cl439, cl440, cl441, \  
        cl442, cl443, cl444, cl445, cl446, cl447, cl448, cl449, cl450, cl451, \  
        cl452, cl453, cl454, cl455, cl456, cl457, cl458, cl459, cl460, cl461, \  
        cl462, cl463, cl464, cl465, cl466, cl467, cl468, cl469, cl470, cl471, \  
        cl472, cl473, cl474, cl475, cl476, cl477, cl478, cl479, cl480, cl481, \  
        cl482, cl483, cl484, cl485, cl486, cl487, cl488  
  
FOR_EACH(FAKE_HEADER, LIST)  
  
#include <boost/range/iterator_range_core.hpp>  
  
int main() {}  
```  
  
My original environment uses boost 1.83.0, and I've tried that 1.87.0, but it has the same issue. g++ 11.4.0, g++ 13.1.0 and g++ 14.2.0 all cause the error too.  
  
```  
   93 |       BOOST_PP_CAT(boost_concept_check,__LINE__)  
```  
  
boost_concept_check93 is maybe generated in this line, with `__LINE__ == 93`. Interestingly, line 93 is exactly this line. I think somehow the preprocessor replaced the wrong line number with `__LINE__` and caused this.  
  
The code contains a long `LIST` and a `FAKE_HEADER`. `LIST` contains 489 entries. If removing any one entry in the list (making it less than 489 entries), the code will compile. If you remove any function in the fake header, the code will compile as well. I think it has some relation between the number of total tokens in the macro. It could be a bug or limitation from gcc.

---

## Comment 1

> Username: mclow  
> Created at: 2024-12-24 14:33:33 UTC  
> Updated at: 2024-12-24 14:33:58 UTC  
> Url: https://github.com/boostorg/boost/issues/994#issuecomment-2561189106  

This compiles w/o an error for me using "Apple clang version 16.0.0 (clang-1600.0.26.6)" and the latest boost release.

---

## Comment 2

> Username: edisonhello  
> Created at: 2024-12-24 16:31:05 UTC  
> Url: https://github.com/boostorg/boost/issues/994#issuecomment-2561275478  

Instead of including `iterator_range_core.hpp`, replace it with  
  
```  
#include <boost/mpl/aux_/preprocessor/default_params.hpp>  
#include <boost/mpl/apply_fwd.hpp>  
#include <boost/mpl/lambda.hpp>  
  //  
  
#include <boost/iterator/iterator_concepts.hpp>  
#include <boost/range/end.hpp>  
  
  
namespace boost :: mpl {  
  
template< typename Value > struct always  
{  
    template<  
        BOOST_MPL_PP_DEFAULT_PARAMS(5, typename T, na)  
        >  
    struct apply  
    {  
    };  
};  
  
}  
  
namespace boost :: range_detail {  
  
#define BOOST_RANGE_CONCEPT_ASSERT( x ) BOOST_CONCEPT_ASSERT( x )  
  
    template<class Iterator>  
    struct IncrementableIteratorConcept : CopyConstructible<Iterator>  
    {  
        typedef BOOST_DEDUCED_TYPENAME iterator_traversal<Iterator>::type traversal_category;  
  
        BOOST_RANGE_CONCEPT_ASSERT((  
            Convertible<  
                traversal_category,  
                incrementable_traversal_tag  
            >));  
  
        BOOST_CONCEPT_USAGE(IncrementableIteratorConcept)  
        {  
        }  
    };  
  
    template<class Iterator>  
    struct SinglePassIteratorConcept  
        : IncrementableIteratorConcept<Iterator>  
        , EqualityComparable<Iterator>  
    {  
        BOOST_RANGE_CONCEPT_ASSERT((  
            Convertible<  
                BOOST_DEDUCED_TYPENAME SinglePassIteratorConcept::traversal_category,  
                single_pass_traversal_tag  
            >));  
  
        BOOST_CONCEPT_USAGE(SinglePassIteratorConcept)  
        {  
        }  
    };  
  
    template<class Iterator>  
    struct ForwardIteratorConcept  
        : SinglePassIteratorConcept<Iterator>  
        , DefaultConstructible<Iterator>  
    {  
        typedef BOOST_DEDUCED_TYPENAME std::iterator_traits<Iterator>::difference_type difference_type;  
  
        BOOST_MPL_ASSERT((is_integral<difference_type>));  
        BOOST_MPL_ASSERT_RELATION(std::numeric_limits<difference_type>::is_signed, ==, true);  
  
        BOOST_RANGE_CONCEPT_ASSERT((  
            Convertible<  
                BOOST_DEDUCED_TYPENAME ForwardIteratorConcept::traversal_category,  
                forward_traversal_tag  
            >));  
  
        BOOST_CONCEPT_USAGE(ForwardIteratorConcept)  
        {  
        }  
    };  
  
  
} // namespace range_detail  
```  
  
also produce the same error. It might be easier to inspect what happened.  
  
Also, clang++-19 compiles the code without any error. It seems like a gcc-only issue.
