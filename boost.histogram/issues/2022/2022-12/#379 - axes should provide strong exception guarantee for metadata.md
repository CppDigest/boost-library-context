# #379 - axes should provide strong exception guarantee for metadata [Open]

> Username: HDembinski  
> Created at: 2022-12-24 13:49:35 UTC  
> Updated at: 2023-01-11 10:52:05 UTC  
> Url: https://github.com/boostorg/histogram/issues/379  

`template <class It, class = detail::requires_iterator<It>> variable(It begin, It end, metadata_type meta = {}, options_type options = {}, allocator_type alloc = {})` does not provide strong exception guarantee: if user writes `variable(it_begin, it_end, std::move(str))` and constructor body throws then the value inside `str` is lost (the value was moved into parameter and then data member before the exception). So one can not write `while(std::cin>>str>>...){ try{ variable(..., ..., str); ... }catch(...){} }` and has to take on the burden of saving the `str` elsewhere (so the user do not have to type it again) when other constructor arguments might be illegal and have to be typed again.  
  
_Originally posted by @jhcarl0814 in https://github.com/boostorg/histogram/issues/372#issuecomment-1364386000_

---

## Comment 1

> Username: jhcarl0814  
> Created at: 2022-12-25 05:50:45 UTC  
> Updated at: 2022-12-25 06:21:49 UTC  
> Url: https://github.com/boostorg/histogram/issues/379#issuecomment-1364630358  

@HDembinski Here is an example of [constructor rollback "`std::move`s from parameters to data members" on exception](https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGIAOykrgAyeAyYAHI%2BAEaYxCBm0gAOqAqETgwe3r4BpClpjgKh4VEssfGJtpj2hQxCBEzEBFk%2BfoF2mA4Z9Y0ExZExcQnSCg1NLTntY31hA2VDiQCUtqhexMjsHOYAzGHI3lgm224EAJ5JmAD6BMRMhApH2CYaAII7eweYR25Oo8SYrEezzeZl2DH2XkOx2iqFSBAA9Gh/vCvOCmF5gAgCJdMKoNklagoAHQIJJJIGvYEETAsJIGanfM4XZhsADUABUKS8/l4HKz/ixUAA3K7EIViLyizBUOKMDbAkz%2BKyvVmq1leNJGVlMzCso4AEW150YrF1o3QIBQAnwtTE12%2B5steAUlzFEqlMv%2B4K%2Bx0522wlvF3kwpFZjpAAuFHtl3vtvseob9T22yre/n1R1T1Np9J9J2NLN1SeBGrCwH5NKjrqDktd0pjG2ueu2hp1hYrgpF1fddc9crzSctOszCte2bpTAZxzbpo5QnWoZnbPZ%2BtGXJ5fOInlo0SYyAA1pdO1cBDi8ZgCRkFUrgWr1Qw0sBwuhWWECKy0KjsSOVWrw5Gu3%2BPtYwIb52XnUx/VZAAqBR1kuH8Xjvf9K0A%2BsvUbUDfVXLDsBg9BRgQlNRyQtUt1oHc90PY9LlPXF8VqCAUJooCGyuLC3HA9ZHhguDkFDZiq1YjD2LAnCeOggiCEWW87zVEAP1WQRLggGE4UtJF2BAVFRAxLEzwYjIFAgRZllkuS/3gpj1jM38LNVKSVKkmS7L1G90xIu8AD9yMog8jyrOjz0vAQTPMtzU3s18qAgT9lNZMAwANVk1NGDSSC0nT0UxbF6IvQkTJc0iLMVSKorVWD4ObQ1w2PCBJMImTiNcu9FQzVy2uvDNmpecdc0ZAtZy4/ijWZIbxP9YEN3fXzd38migsMgQwIgvUzAANkTCankpG9XNmqiAq7Rb8oyJiCAtCNUOjES404iCJIEi7LQAm7%2Bzulc1yg6DFgizrdu61MFVBLAaHCVkACUAHlgmCCwXjcABpS4AFkoYANWwS4oYiS5sAADTcbBlHZABJHGICSRpTWpYgzySUM2HmOncSSX6DvmwKGAM07luOV7u2DXs2LurB9h1SnqbYWn6aa7BQzF2gJaZ8pZZ4l1gC8Rp0Agcx1qp25pbienQz1lXjdZoqppuXl32QLh7V2srVXDP4yzDIjnbDZ6QDdow9eATBsWMmSlX%2BAg1gYD2Rw81y31ZbnEPCvrJzzJddXqYguTve3rggTO1vWvWFFDNTaG1BBiAAdyKqKFJdc7Lv4avte%2BTPHiY0zSHCizud1gBWKwNpMfv2uK8rSp78rVWh2H4aR1GMaxnG8cJ4myYpkvWUD4PCsQ6fVXheE1VnuGEeRtHMex3GCaJknyYiJiBMuJqvenvAYoISua4rrcq9ZLg%2B8D6qnDpHVkGggHTzanvDqe1x53lPvPC%2BS9r6rzvhvR%2BW8XSvynqqD%2BEAv7V1%2BoQ1A/9AE9VarHNMgMSLxxYHcBgEB46NGACNZACBGgwRYUKEeVhR5WzgZQt%2Bf4fZ%2B3LMZcwZgmDRFMGYMwOCWpqlznbLgT9WRUDEEoBR8CRGXU/O%2Bb43wAHNjcEYhQJijFgC2P3NwDArGQLcmPIRuDvaXTEWGXWcjpGyPkQ4nODsVGN0tHVBQyx1GaMwNoqK4Z9EWOOKyMwcS3BhiSQlaxtj7EULVP9cek9FEu1ETcd2EivEyMkVE%2ByNxTguLyToiyyiPyqK3jbSJfjslUKiqIAg7CIBEj6bXeytSoo5OiT7WJhj4nbFSeYiZyT7E2LsVsLJqoRntOEQUtxRStQlKkWUuRFSLJVJqYIg%2BDT7ZBJACEsJLSDlyVWfUycPS%2BlEgGSVE5bynH2RiasAxxwjGSGmak%2BZGSllexyTkjgyxaCcH7rwPwHAtCkFQJwUxlhrBhlWOsXUOweCkAIJoSFyx9wgH7hofQnBJC8BYCAdaAAOIkABOSQkgNAMoZdsfwtKuVcDZaQeFiLkUcF4AoEAZL8UIshaQOAsAkBoFpHQOI5BKByqSAq%2BIwAFDMCSAoBAqACCkCwEKPAGx0Z4EwFXKGzJOC4poErOIIrVIEtINEMIjRTjWt4HKtgggoYMFoO6iVBqaSGGAOIQN%2BB/hdBFCKwNuJOheGpB68gghqhOtoHgaItxiCnA8FgJ1RTqXcElVQAwGrTXmstYwJN/BBAiDEOwKQMhBCKBUOoQNuguD6BDSgaw1h9AZpFbAQsIBUr6pFPEbgtKtBd1QCFB8nAAC05oDSyKHhYKQrIF1Q0SQugA6mIcuC6LjoEMI4ZAm6CSVwBOgYV1ROi1BcAwdwnhWh6BCLMUo5Q9D5HSAICYfhO0/tqP0T9QxO0dC6AIHo4wX05HA3eyDdRpggcGPEcD0x/16FGL0FDzMJDLAUJijY%2BHyUcFhXyp1grWSqFpetBd60AWsPPQQ4gqJ9y/QgLgQgJA1rbC4IsXg4rp1SqQHG5ACaSBKogCwhQyhDDVCELqqu8LcUqroJODIcnwi0EU6Q/lnrUDyvoOqzVTBtW6v1Wp4zUME26eU06sTLxiAas4LwMT9R8Dwt4DW4QohxCNp8y2tQTqO1dqMD2tFlh%2B3REHVAYdo7SDjutVOwlSK50xqXRdFdvbLAbq3Tu/dFEL2YBPYIY1F7CHXuFURhttgLphC0wppTKnBP/E2LiqutwkgeqhTCuFlHODYFUPGogxBqO0fo4x5AzGbYMHY6yCAqK12hi46N3j/HBMEq7gga9QwTKkcpaQalfGiQaH7v3BlGgNDrTMFwO7U6uCBH00i1ztg9BCdS8SrgV3SPbH64GwVm2JVd3HWkZwkggA%3D%3D):  
```C++  
#include<type_traits>  
#include<iostream>  
#include<boost/core/uncaught_exceptions.hpp>  
  
template<typename T>  
struct remove_rvalue_reference  
{  
    using type = typename std::conditional_t<std::is_rvalue_reference<T>::value, std::remove_reference_t<T>, T>;  
};  
template<typename T>  
using remove_rvalue_reference_t = typename remove_rvalue_reference<T>::type;  
  
template<typename TSrc, typename TDst>  
struct rollback_move_on_exception  
{  
    unsigned int count_;  
    std::remove_reference_t<TSrc> *src_;  
    std::remove_reference_t<TDst> *dst_;  
  
    rollback_move_on_exception(std::remove_reference_t<TSrc> *src, std::remove_reference_t<TDst> *dst)  
        : count_(boost::core::uncaught_exceptions()),  
          src_(src),  
          dst_(dst)  
    {}  
  
    ~rollback_move_on_exception()  
    {  
        if(count_ != boost::core::uncaught_exceptions())  
        {  
            *src_ = std::move(*dst_);  
        }  
    }  
};  
template<typename TSrc, typename TDst>  
struct rollback_move_on_exception<TSrc &, TDst>  
{  
    rollback_move_on_exception(std::remove_reference_t<TSrc> *, std::remove_reference_t<TDst> *) {}  
};  
  
#define ROLLBACK_MOVE_ON_EXCEPTION(parameter_exp, member_exp) rollback_move_on_exception<remove_rvalue_reference_t<decltype(parameter_exp)>, decltype(member_exp)> s_guard(&parameter_exp, &member_exp)  
  
struct c1_t  
{  
    std::string s_;  
    std::string&get_s(){return s_;}  
    int n_;  
  
    template<typename Str>  
    c1_t(Str &&s, bool thrw)  
        : s_(std::forward<Str>(s)),  
          n_([&]  
            {  
                ROLLBACK_MOVE_ON_EXCEPTION(s, get_s());  
            //   ROLLBACK_MOVE_ON_EXCEPTION(s, s_);  
              if(thrw) throw 1;  
              return 0;  
            }())  
    {  
        ROLLBACK_MOVE_ON_EXCEPTION(s, s_);  
        if(thrw) throw 1;  
    }  
};  
  
int main(int argc, char *argv[])  
{  
    {  
        std::string s("abc");  
        c1_t c1(s, false);  
        std::cout << 1 << s << '\n';  
    }  
    {  
        std::string s("abc");  
        c1_t c1(std::move(s), false);  
        std::cout << 2 << s << '\n';  
    }  
    {  
        std::string s("abc");  
        try  
        {  
            c1_t c1(s, true);  
        }  
        catch(...)  
        {  
        }  
        std::cout << 3 << s << '\n';  
    }  
    {  
        std::string s("abc");  
        try  
        {  
            c1_t c1(std::move(s), true);  
        }  
        catch(...)  
        {  
        }  
        std::cout << 4 << s << '\n';  
    }  
}  
```  
```  
1abc  
2  
3abc  
4abc  
```  
  
good:  
  
- types of bases/data_members don't have to be DefaultConstructible (useful in general but not relevant to `axis::variable`, its bases/data_members are already DefaultConstructible)  
- preserves the initialization order of bases/data_members  
  
bad:  
  
- for each base/data_member A (that is move constructed):  
  - for each base/data_member B that is declared after A and whose construction might throw:  
    - programmer has to change to `[&]{ ... }()` to initialize B and repeat that macro for A in the lambda  
  - programmer has to repeat that macro for A in the constructor body (even if the constructor body is empty, because it's too easy to forget to add it in the future)  
  
not possible to cover:  
  
- when `It begin, It end` gives prvalues (e.g. std::istream_iterator)  
  
too troublesome to cover:  
  
- when `It begin, It end` gives xvalues (e.g. std::move_iterator)  
  
modified version of `include\boost\histogram\axis\variable.hpp`:  
```C++  
// Copyright 2015-2018 Hans Dembinski  
//  
// Distributed under the Boost Software License, Version 1.0.  
// (See accompanying file LICENSE_1_0.txt  
// or copy at http://www.boost.org/LICENSE_1_0.txt)  
  
#ifndef BOOST_HISTOGRAM_AXIS_VARIABLE_HPP  
#define BOOST_HISTOGRAM_AXIS_VARIABLE_HPP  
  
#include <algorithm>  
#include <boost/core/nvp.hpp>  
#include <boost/histogram/axis/interval_view.hpp>  
#include <boost/histogram/axis/iterator.hpp>  
#include <boost/histogram/axis/metadata_base.hpp>  
#include <boost/histogram/axis/option.hpp>  
#include <boost/histogram/detail/convert_integer.hpp>  
#include <boost/histogram/detail/detect.hpp>  
#include <boost/histogram/detail/eytzinger_search.hpp>  
#include <boost/histogram/detail/limits.hpp>  
#include <boost/histogram/detail/relaxed_equal.hpp>  
#include <boost/histogram/detail/replace_type.hpp>  
#include <boost/histogram/fwd.hpp>  
#include <boost/throw_exception.hpp>  
#include <cassert>  
#include <cmath>  
#include <limits>  
#include <memory>  
#include <stdexcept>  
#include <string>  
#include <type_traits>  
#include <utility>  
#include <vector>  
  
#include <boost/core/uncaught_exceptions.hpp>  
  
template <typename T>  
struct remove_rvalue_reference {  
  using type = typename std::conditional_t<std::is_rvalue_reference<T>::value,  
                                           std::remove_reference_t<T>, T>;  
};  
template <typename T>  
using remove_rvalue_reference_t = typename remove_rvalue_reference<T>::type;  
  
template <typename TSrc, typename TDst>  
struct rollback_move_on_exception {  
  unsigned int count_;  
  std::remove_reference_t<TSrc>* src_;  
  std::remove_reference_t<TDst>* dst_;  
  
  rollback_move_on_exception(std::remove_reference_t<TSrc>* src,  
                             std::remove_reference_t<TDst>* dst)  
      : count_(boost::core::uncaught_exceptions()), src_(src), dst_(dst) {}  
  
  ~rollback_move_on_exception() {  
    if (count_ != boost::core::uncaught_exceptions()) { *src_ = std::move(*dst_); }  
  }  
};  
template <typename TSrc, typename TDst>  
struct rollback_move_on_exception<TSrc&, TDst> {  
  rollback_move_on_exception(std::remove_reference_t<TSrc>*,  
                             std::remove_reference_t<TDst>*) {}  
};  
  
#define ROLLBACK_MOVE_ON_EXCEPTION(parameter_exp, member_exp)                    \  
  rollback_move_on_exception<remove_rvalue_reference_t<decltype(parameter_exp)>, \  
                             decltype(member_exp)>                               \  
      s_guard(&parameter_exp, &member_exp)  
  
namespace boost {  
namespace histogram {  
namespace axis {  
  
/** Axis for non-equidistant bins on the real line.  
  
  Binning is a O(log(N)) operation. If speed matters and the problem domain  
  allows it, prefer a regular axis, possibly with a transform.  
  
  If the axis has an overflow bin (the default), a value on the upper edge of the last  
  bin is put in the overflow bin. The axis range represents a semi-open interval.  
  
  If the overflow bin is deactivated, then a value on the upper edge of the last bin is  
  still counted towards the last bin. The axis range represents a closed interval. This  
  is the desired behavior for random numbers drawn from a bounded interval, which is  
  usually closed.  
  
  @tparam Value     input value type, must be floating point.  
  @tparam MetaData  type to store meta data.  
  @tparam Options   see boost::histogram::axis::option.  
  @tparam Allocator allocator to use for dynamic memory management.  
*/  
template <class Value, class MetaData, class Options, class Allocator>  
class variable : public iterator_mixin<variable<Value, MetaData, Options, Allocator>>,  
                 public metadata_base_t<MetaData> {  
  // these must be private, so that they are not automatically inherited  
  using value_type = Value;  
  using metadata_base = metadata_base_t<MetaData>;  
  using metadata_type = typename metadata_base::metadata_type;  
  using options_type =  
      detail::replace_default<Options, decltype(option::underflow | option::overflow)>;  
  using allocator_type = Allocator;  
  using vector_type = std::vector<Value, allocator_type>;  
  
public:  
  constexpr variable() = default;  
  explicit variable(allocator_type alloc) : vec_(alloc) {}  
  
  /** Construct from iterator range of bin edges.  
  
     @param begin   begin of edge sequence.  
     @param end     end of edge sequence.  
     @param meta    description of the axis (optional).  
     @param options see boost::histogram::axis::option (optional).  
     @param alloc   allocator instance to use (optional).  
   */  
  template <class It, class Metadata = metadata_type, class = detail::requires_iterator<It>>  
  variable(It begin, It end, Metadata&& meta = {}, options_type options = {},  
           allocator_type alloc = {})  
      : metadata_base(std::forward<Metadata>(meta))  
      , vec_([&] {  
        ROLLBACK_MOVE_ON_EXCEPTION(meta, this->metadata());  
        if (std::distance(begin, end) < 2)  
          BOOST_THROW_EXCEPTION(std::invalid_argument("bins > 0 required"));  
  
        vector_type vec(std::move(alloc));  
  
        vec.reserve(std::distance(begin, end));  
        vec.emplace_back(*begin++);  
        bool strictly_ascending = true;  
        for (; begin != end; ++begin) {  
          strictly_ascending &= vec.back() < *begin;  
          vec.emplace_back(*begin);  
        }  
        if (!strictly_ascending)  
          BOOST_THROW_EXCEPTION(  
              std::invalid_argument("input sequence must be strictly ascending"));  
        return vec;  
      }())  
      , eytzinger_layout_and_eytzinger_binary_search_([&] {  
        ROLLBACK_MOVE_ON_EXCEPTION(meta, this->metadata());  
        return decltype(eytzinger_layout_and_eytzinger_binary_search_)(vec_);  
      }()) {  
    ROLLBACK_MOVE_ON_EXCEPTION(meta, this->metadata());  
    // static_asserts were moved here from class scope to satisfy deduction in gcc>=11  
    static_assert(  
        std::is_floating_point<value_type>::value,  
        "current version of variable axis requires floating point type; "  
        "if you need a variable axis with an integral type, please submit an issue");  
    static_assert((!options.test(option::circular) && !options.test(option::growth)) ||  
                      (options.test(option::circular) ^ options.test(option::growth)),  
                  "circular and growth options are mutually exclusive");  
  }  
  
  // kept for backward compatibility; requires_allocator is a workaround for deduction  
  // guides in gcc>=11  
  template <class It, class Metadata, class A, class = detail::requires_iterator<It>,  
            class = detail::requires_allocator<A>>  
  variable(It begin, It end, Metadata&& meta, A alloc)  
      : variable(begin, end, std::forward<Metadata>(meta), {}, std::move(alloc)) {}  
  
  /** Construct variable axis from iterable range of bin edges.  
  
     @param iterable iterable range of bin edges.  
     @param meta     description of the axis (optional).  
     @param options  see boost::histogram::axis::option (optional).  
     @param alloc    allocator instance to use (optional).  
   */  
  template <class U, class Metadata = metadata_type, class = detail::requires_iterable<U>>  
  variable(const U& iterable, Metadata&& meta = {}, options_type options = {},  
           allocator_type alloc = {})  
      : variable(std::begin(iterable), std::end(iterable), std::forward<Metadata>(meta),  
                 options, std::move(alloc)) {}  
  
  // kept for backward compatibility; requires_allocator is a workaround for deduction  
  // guides in gcc>=11  
  template <class U, class Metadata, class A, class = detail::requires_iterable<U>,  
            class = detail::requires_allocator<A>>  
  variable(const U& iterable, Metadata&& meta, A alloc)  
      : variable(std::begin(iterable), std::end(iterable), std::forward<Metadata>(meta),  
                 {}, std::move(alloc)) {}  
  
  /** Construct variable axis from initializer list of bin edges.  
  
     @param list     `std::initializer_list` of bin edges.  
     @param meta     description of the axis (optional).  
     @param options  see boost::histogram::axis::option (optional).  
     @param alloc    allocator instance to use (optional).  
   */  
  template <class U, class Metadata = metadata_type>  
  variable(std::initializer_list<U> list, Metadata&& meta = {},  
           options_type options = {}, allocator_type alloc = {})  
      : variable(list.begin(), list.end(), std::forward<Metadata>(meta), options,  
                 std::move(alloc)) {}  
  
  // kept for backward compatibility; requires_allocator is a workaround for deduction  
  // guides in gcc>=11  
  template <class U, class Metadata, class A, class = detail::requires_allocator<A>>  
  variable(std::initializer_list<U> list, Metadata&& meta, A alloc)  
      : variable(list.begin(), list.end(), std::forward<Metadata>(meta), {},  
                 std::move(alloc)) {}  
  
  /// Constructor used by algorithm::reduce to shrink and rebin (not for users).  
  variable(const variable& src, index_type begin, index_type end, unsigned merge)  
      : metadata_base(src)  
      , vec_([&] {  
        assert((end - begin) % merge == 0);  
        if (options_type::test(option::circular) && !(begin == 0 && end == src.size()))  
          BOOST_THROW_EXCEPTION(std::invalid_argument("cannot shrink circular axis"));  
  
        vector_type vec(src.get_allocator());  
        vec.reserve((end - begin) / merge);  
        const auto beg = src.vec_.begin();  
        for (index_type i = begin; i <= end; i += merge) vec.emplace_back(*(beg + i));  
        return vec;  
      }())  
      , eytzinger_layout_and_eytzinger_binary_search_(vec_) {}  
  
  /// Return index for value argument.  
  index_type index(value_type x) const noexcept {  
    if (options_type::test(option::circular)) {  
      const auto a = vec_[0];  
      const auto b = vec_[size()];  
      x -= std::floor((x - a) / (b - a)) * (b - a);  
    }  
    // upper edge of last bin is inclusive if overflow bin is not present  
    if (!options_type::test(option::overflow) && x == vec_.back()) return size() - 1;  
    return eytzinger_layout_and_eytzinger_binary_search_.index(x);  
  }  
  
  std::pair<index_type, index_type> update(value_type x) noexcept {  
    const auto i = index(x);  
    if (std::isfinite(x)) {  
      if (0 <= i) {  
        if (i < size()) return std::make_pair(i, 0);  
        const auto d = value(size()) - value(size() - 0.5);  
        x = std::nextafter(x, (std::numeric_limits<value_type>::max)());  
        x = (std::max)(x, vec_.back() + d);  
        vec_.push_back(x);  
        eytzinger_layout_and_eytzinger_binary_search_.assign(vec_);  
        return {i, -1};  
      }  
      const auto d = value(0.5) - value(0);  
      x = (std::min)(x, value(0) - d);  
      vec_.insert(vec_.begin(), x);  
      eytzinger_layout_and_eytzinger_binary_search_.assign(vec_);  
      return {0, -i};  
    }  
    return {x < 0 ? -1 : size(), 0};  
  }  
  
  /// Return value for fractional index argument.  
  value_type value(real_index_type i) const noexcept {  
    if (options_type::test(option::circular)) {  
      auto shift = std::floor(i / size());  
      i -= shift * size();  
      double z;  
      const auto k = static_cast<index_type>(std::modf(i, &z));  
      const auto a = vec_[0];  
      const auto b = vec_[size()];  
      return (1.0 - z) * vec_[k] + z * vec_[k + 1] + shift * (b - a);  
    }  
    if (i < 0) return detail::lowest<value_type>();  
    if (i == size()) return vec_.back();  
    if (i > size()) return detail::highest<value_type>();  
    const auto k = static_cast<index_type>(i); // precond: i >= 0  
    const real_index_type z = i - k;  
    // check z == 0 needed to avoid returning nan when vec_[k + 1] is infinity  
    return (1.0 - z) * vec_[k] + (z == 0 ? 0 : z * vec_[k + 1]);  
  }  
  
  /// Return bin for index argument.  
  auto bin(index_type idx) const noexcept { return interval_view<variable>(*this, idx); }  
  
  /// Returns the number of bins, without over- or underflow.  
  index_type size() const noexcept { return static_cast<index_type>(vec_.size()) - 1; }  
  
  /// Returns the options.  
  static constexpr unsigned options() noexcept { return options_type::value; }  
  
  template <class V, class M, class O, class A>  
  bool operator==(const variable<V, M, O, A>& o) const noexcept {  
    const auto& a = vec_;  
    const auto& b = o.vec_;  
    return std::equal(a.begin(), a.end(), b.begin(), b.end()) &&  
           detail::relaxed_equal{}(this->metadata(), o.metadata());  
  }  
  
  template <class V, class M, class O, class A>  
  bool operator!=(const variable<V, M, O, A>& o) const noexcept {  
    return !operator==(o);  
  }  
  
  /// Return allocator instance.  
  auto get_allocator() const { return vec_.get_allocator(); }  
  
  template <class Archive>  
  void serialize(Archive& ar, unsigned /* version */) {  
    ar& make_nvp("seq", vec_);  
    ar& make_nvp("meta", this->metadata());  
  }  
  
private:  
  vector_type vec_;  
  boost::histogram::detail::eytzinger_layout_and_eytzinger_binary_search_t<Value>  
      eytzinger_layout_and_eytzinger_binary_search_;  
  
  template <class V, class M, class O, class A>  
  friend class variable;  
};  
  
#if __cpp_deduction_guides >= 201606  
  
template <class T>  
variable(std::initializer_list<T>)  
    -> variable<detail::convert_integer<T, double>, null_type>;  
  
template <class T, class M>  
variable(std::initializer_list<T>, M)  
    -> variable<detail::convert_integer<T, double>,  
                detail::replace_type<std::decay_t<M>, const char*, std::string>>;  
  
template <class T, class M, unsigned B>  
variable(std::initializer_list<T>, M, const option::bitset<B>&)  
    -> variable<detail::convert_integer<T, double>,  
                detail::replace_type<std::decay_t<M>, const char*, std::string>,  
                option::bitset<B>>;  
  
template <class Iterable, class = detail::requires_iterable<Iterable>>  
variable(Iterable) -> variable<  
    detail::convert_integer<  
        std::decay_t<decltype(*std::begin(std::declval<Iterable&>()))>, double>,  
    null_type>;  
  
template <class Iterable, class M>  
variable(Iterable, M) -> variable<  
    detail::convert_integer<  
        std::decay_t<decltype(*std::begin(std::declval<Iterable&>()))>, double>,  
    detail::replace_type<std::decay_t<M>, const char*, std::string>>;  
  
template <class Iterable, class M, unsigned B>  
variable(Iterable, M, const option::bitset<B>&) -> variable<  
    detail::convert_integer<  
        std::decay_t<decltype(*std::begin(std::declval<Iterable&>()))>, double>,  
    detail::replace_type<std::decay_t<M>, const char*, std::string>, option::bitset<B>>;  
  
#endif  
  
} // namespace axis  
} // namespace histogram  
} // namespace boost  
  
#endif  
```

---

## Comment 2

> Username: HDembinski  
> Created at: 2022-12-26 10:10:25 UTC  
> Url: https://github.com/boostorg/histogram/issues/379#issuecomment-1365057784  

The rollback_move_on_exception guard is cool, but maybe we fix it without using that.

---

## Comment 3

> Username: HDembinski  
> Created at: 2022-12-26 11:12:39 UTC  
> Updated at: 2022-12-26 11:55:07 UTC  
> Url: https://github.com/boostorg/histogram/issues/379#issuecomment-1365097978  

I gave this more thought and I don't want to fix this. I don't like that we have to template the Metadata parameter for this to work. There is a compilation cost associated to all the template instantiations and we try to keep that minimal, too.  
  
Instead, the caveat of the design will be documented in the docstring of the ctor.

---

## Comment 4

> Username: HDembinski  
> Created at: 2022-12-26 13:04:48 UTC  
> Url: https://github.com/boostorg/histogram/issues/379#issuecomment-1365152468  

We don't have to worry about moving from the iterators, because the iterators will only ever by simple arithmetic types, were moves do not give you an advantage.

---

## Comment 5

> Username: HDembinski  
> Created at: 2022-12-26 14:17:42 UTC  
> Updated at: 2022-12-26 14:41:19 UTC  
> Url: https://github.com/boostorg/histogram/issues/379#issuecomment-1365217704  

@jhcarl0814 What do you think about this design, which is based on your rollback_move_on_exception idea. It delays the move until the constructor has completed and only performs it if there was no exception. I have to use a helper type in the public API `delayed_forward`, but this also has an advantage: the implictly generated deduction guide is now no longer valid, and now even gcc picks up the custom deduction guide. The implementation also delays copies until the axis ctor succeeded.  
  
The change of initialization order is a not a problem, since initialization order is an implementation detail. We turn an move/copy-ctor into default ctor + move/copy-assign. That's ok, the MetaData type must be assignable and default constructible.   
  
The ultimate conclusion of Hyrum's law is that you cannot change anything ever. Boost does not follow that, because we want to keep some room for improvements. We are not the C++ stdlib. That being said, any change to the API has to be done with care and must not break existing code (without a deprecation period and prior warning).  
  
[Godbolt](https://godbolt.org/z/nq7oTaTWd)  
```c++  
#include <iostream>  
#include <cassert>  
#include <stdexcept>  
#include <boost/core/lightweight_test.hpp>  
  
struct meta {  
    void msg(const char* msg) {  
        std::cerr << msg << std::endl;  
    }  
  
    meta() { msg("Default ctor"); }  
    meta(int v) : value_{v} { msg("Init ctor"); }  
    meta(const meta& o) : value_{o.value_} { msg("Copy ctor"); }  
    meta( meta&& o) noexcept {  
        msg("Move ctor");   
        value_ = o.value_;  
        o.value_ = 0;  
    }  
    meta& operator=(meta&& o) noexcept {  
        msg("Move assign");  
        value_ = o.value_;  
        o.value_ = 0;  
        return *this;  
    }  
    meta& operator=(const meta& o) {   
        msg("Copy assign");  
        value_ = o.value_;  
        return *this;  
    }  
  
    ~meta() {  
        if (value_)  
           msg("Destroy");  
    }  
  
    int value_ = 0;  
};  
  
template <class T>  
struct delayed_forward {  
    delayed_forward() = default;  
    delayed_forward(T&& t) : src_{&t} {}  
    delayed_forward(const T& t) : src_{const_cast<T*>(&t)}, move_{false} {}  
  
    void operator()(T& dst) {  
        if (move_)  
            dst = std::move(*src_);  
        else  
            dst = *src_;  
    }  
  
    T* src_ = nullptr;  
    bool move_ = true;  
};  
  
template <class Meta>  
struct axis {  
    using metadata_type = Meta;  
  
    axis(unsigned n, delayed_forward<metadata_type> meta = {}) {  
        if (n == 0)  
            throw std::invalid_argument("bad argument");  
        meta(meta_); // must come after potential exceptions  
    }  
  
private:  
    metadata_type meta_;  
};  
  
// deduction guide  
template <class M>  
axis(unsigned, M) -> axis<M>;  
  
int main() {  
  
    {   // move with throw  
        meta m = 1;  
        try {  
            axis(0, std::move(m));  
        } catch(...) {  
            BOOST_TEST_EQ(m.value_, 1);  
        }  
    }  
  
    {   // move without throw  
        meta m = 1;  
        axis(1, std::move(m));  
        BOOST_TEST_EQ(m.value_, 0);  
    }  
  
    {   // copy with throw  
        meta m = 1;  
        try {  
            axis(0, m);  
        } catch(...) {  
            BOOST_TEST_EQ(m.value_, 1);  
        }  
    }  
  
    {   // copy without throw  
        meta m = 1;  
        axis(1, m);  
        BOOST_TEST_EQ(m.value_, 1);  
    }  
  
    // deduction guide  
    axis(1, meta{1});  
  
    return boost::report_errors();  
}  
```

---

## Comment 6

> Username: jhcarl0814  
> Created at: 2022-12-26 22:13:25 UTC  
> Url: https://github.com/boostorg/histogram/issues/379#issuecomment-1365485097  

@HDembinski "moving from the iterators" is okay, but "moving from the things the iterators point to" is-a side-effect:  
  
- `... = *it`/`vec_.emplace_back(*it)` has a side effect if `It` is a specialization of `std::move_iterator`, you might want to save `*it`'s address and move the content back to it when leaving the constructor via exception (storing a growing list of pointers might also throw an exception)  
- `it++` has a side effect if `It` is a specialization of `std::istream_iterator`, you might want to [startTransaction](https://doc.qt.io/qt-6/qiodevice.html#startTransaction) before reading from the stream, [commitTransaction](https://doc.qt.io/qt-6/qiodevice.html#commitTransaction) when leaving the constructor normally, [rollbackTransaction](https://doc.qt.io/qt-6/qiodevice.html#rollbackTransaction) when leaving the constructor via exception :)  
  
---  
  
The `delayed_forward` solution is cool! It also eliminates the need to update constructors' template parameter lists (and ... default template arguments). Here's a version that [added a check against `delayed_forward` default-init case](https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGIAKykrgAyeAyYAHI%2BAEaYxCAAHABspAAOqAqETgwe3r4BaRlZAqHhUSyx8cm2mPaOAkIETMQEuT5%2BgXaYDtmNzQSlkTFxiSkKTS1t%2BZ0TA2FDFSPJAJS2qF7EyOwc5gDMYcjeWADUJrtuTuPEmKxn2CYaAIJ7B0eYp%2BeiCkotdw/PZn2DEOXhOZzc4ywqi2qQIfyeL2Bbw%2BbmiqAyBAA9GhrpjaHhgAgCAB3TAEokAfQImHGADoEKlUvDnk8rl4HMc2E1TgB2KxPY6C44AN1QeHQnIUwAgaAY42OyAQzQAVJLgMtefzHkKdcdISAUHFiCjwWqTec9QR0AbXLQzlqdSYeQARf7/HVcpgQDVOixqiDmMzOzBUJheWgEBVEYiB5b23mugVCz0QMKR4UakAisReTAU33Cp3OzX%2BwMASQYhCjJFj8aL7uTmCaMoE8s95iSx1Qmez3jzvtQtOFOf7LpLLClAbMZg8qQAntWY9O47s/fWk4KU5ym0wOx2uxqGKhMNDMLDNQ3daXpwBZVDC94OGvLusb3XDvsUj7Fwcf3P51dL11X8Ry/M5iw0e0gPXbVGyafdUFSOImGjcCIHbMwkgQw9j1Pc9fSAj1J0DO8H2OJhvgJBha0At8dT/PNvy7IdQKguihRAz8mMg2jYKva4CA2BhjmVAgEDwBQ2L4hMgIwztEOQ1DdmdFs5UjOSDxLQjk2I6dZwXCjMmAaiX14q9BQYsDlOYyypPM44BKEkSxIkuyhRgoCAD8Ux9PltMFPAqGOCBLLjdjdQnaVA2DK5UDnGiHXcl03TfNNe3/bipPrXj/mpFhUgMakUUOQzjgAFWZNkOSwAw50wdAKX4YhiWaCUCLfGqmDqhqmpa4h0G9JisFDcM4TMoVOu6xqSD6gaKsw/cCB7BRNnzPzMLhMcCOSjrai6%2Brpua1rVPlebOyW44sxW5A1osWVxgpL4xrcMrlTuKckiWotSE5e9RwsUNaCUIsLx2llpNFcUuyQ4gUJIb0IDO450HGXzEqvQKIGur8wDAcDjgYcNaFhYgwukx0/PC3VAuClg/opMn7PslHI3x/UQDph8IGVbGV3RpnamBqnmflfGedWtyrxginE3BnVXr1VamMJ2hiYIGNxsFNFPF%2Bh8rOLdXcyy5KcqePKCpQ95wRK75jhvHcmPbXZ7lZQ2OSYVQJIvN8vEyIxtyadAUKYKk5yQpj7fg03pI9iSIC8OUqPqgmfsmg7eta8FPSDppQ6Qu4A6YJjtudNH/OOGmIGE8D8Y0RmmcFMTiFQYlLWtEAwg/cUKWaYAfEYAgpzMaImAlXv%2B8EBLy5TT0GfjTFMU5X3IzQNhyKoaljXSalBDwMRjhPGF6jlaCwf%2BVJiDwYdqRAWSdxzkOCDD95Z%2BN11o4X5H6vZY/jj78VMC5UwPlQqVtPgGFtjeZkscFDx0TsZeqP0bwagALQFxgeCKBzsspPDSiwJgYRBrtTlklfmOp1YLmIQ3QUMCIB10lrqEGogCCKggLSdhZcqbSySrLHU7MtjEA1m4cEYAdj%2BDcAwURDDT5kKFLQrgfNT68KFPwo04IRFiIkVI6OFM/SCk/pzd4xJCAIGOE3Fu08HYsCYlwBh5DiCUMpuTcytCNA/XZoY9CyxFFcLHMw1h7DaScOcVeCwAB5MJQgyoUjKtgKJFJsAAEV0IsU/D9BRdieFKKAqowR6jzhSPEZInYOjSFCgMX9Y4xixLrEjOY4kljuTWPxrYzWup5HuKtAaTxLBvGZMFOEyJ0TYnxKSSkyyP16FtO4YKXJQiNEmCKdorUMjymLzQPOKpJizEIGbg0qmnpOQ2P6WYhx3sQntM9rAtxnIfEXKYShAJHDznUOOIM%2BJIzoljJYKk/86S7n2RmTJN8cz8luEKVokpKy3y%2BjWQqRCC5qkIFqTsvZjSi7NOsq02RNCrkQC4D9XpJz3nDLiV85JPyJnHAydMsGfCumGjyecBZSyoUpWkp/LA6Af7ZD/l4ABQEOmF19LYl0dygKOWIMJbW4wDTXHSC0CkRoSCwPFS6DgqxaCcH8LwPwHAtCkFQJwYRlhrB6nWJsK2gIeCkAIJoDVqwADWAQ3Fao4JIXgLAQBJB5LSXYSRdj%2BF2BoHkPIEhcCSJIAAnLsUgeqDVGo4LwBQIA3F2v1Rq0gcBYBIBPN0Lw0ZyCUF7goZQhhahCGRcSPVNrV6pDoChbIZbwi0ErS3eNvA610BGMABQzBUgKGRQQUgXb6DEDCQWtt1b7VBFUN0R4xBe2cF4Hm5AjR8B6t4PwQQIgxDsCkDIQQigVDqAzaQXQBKDBGBQNYaw%2Bg8DRBTbAZgbAQAyuHQ%2BeI3AEhaG8Ya2E2QU0cBQZCcCphTWWC4DyY4KCwm7BgwAdTELQGDsJdk3HQMmi1Ww9CQjCM2itVaa28HVpgbYNriSw1SJwHgmrtW6pnYm7Ac7kAFpIMcVQyQUGRr/sgZAwVDYMEdRqCAJqrCWB%2BrgQg7G9gKJI/av9CAMMjG9PoTgHrSBet2GYWkAbI1JCjdOLgCRA0hrjYx5dthU22oU06iQGhXWcF2Axs9ib5MZr/Z%2BzIzhJBAA%3D%3D%3D).

---

## Comment 7

> Username: HDembinski  
> Created at: 2022-12-28 23:19:19 UTC  
> Updated at: 2022-12-28 23:19:42 UTC  
> Url: https://github.com/boostorg/histogram/issues/379#issuecomment-1366977352  

@jhcarl0814  
  
- Ok, I see the point that `std::move_iterator` could potentially have side effects. For plain arithmetic types, moving typically does the same as copying, since otherwise a move would be more costly than a copy, but that's not guaranteed.  
- `std::istream_iterator` is not allowed, only forward_iterators are allowed. In C++14, we cannot catch this at compile-time with concepts, but it is now properly documented.  
  
> The delayed_forward solution is cool! It also eliminates the need to update constructors' template parameter lists (and ... default template arguments). Here's a version that added a check against delayed_forward default-init case.  
  
Yes that's good. It would be great if we could enforce at compile-time that the developer calls `void operator()(T& dst)` exactly once.

---

## Comment 8

> Username: HDembinski  
> Created at: 2022-12-28 23:39:40 UTC  
> Url: https://github.com/boostorg/histogram/issues/379#issuecomment-1366983012  

I am going to reopen this issue, since it would be good to address this as best as possible. The perfect is the enemy of the good.

---

## Comment 9

> Username: jhcarl0814  
> Created at: 2022-12-29 10:22:30 UTC  
> Updated at: 2022-12-29 10:34:18 UTC  
> Url: https://github.com/boostorg/histogram/issues/379#issuecomment-1367217131  

@HDembinski [Boost.Multiprecision doc](https://www.boost.org/doc/libs/release/libs/multiprecision/doc/html/boost_multiprecision/intro.html#boost_multiprecision.intro.move_semantics) says that `number` (of which `boost::multiprecision::float128` is a specialization) uses move semantics, so moving from a `boost::multiprecision::float128` may have side effect and those move operations need to be delayed or rollbacked in generic (general) when `Value` is unknown.  
But `boost::multiprecision::float128` does not satisfy `std::is_floating_point`/`boost::is_floating_point` (they are used for `type categories`), maybe should use `std::numeric_limits<T>::is_specialized && !std::numeric_limits<T>::is_integer` in `variable` instead?  
  
---  
  
One way to force constructor author invoke the move logic only once is to let constructor author pass the function body to `delayed_forward` and let `delayed_forward` execute the function body and then move logic. Since constructor author is unlikely to write function body twice (which would be an obvious mistake), the move logic is also unlikely to be executed twice.  
  
[example](https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGIAGykrgAyeAyYAHI%2BAEaYxBIapAAOqAqETgwe3r4ByanpAqHhUSyx8VyJdpgOGUIETMQEWT5%2BgVU1AnUNBEWRMXEJtvWNzTltwz1hfaUDFQCUtqhexMjsHOYAzGHI3lgA1CYbbk4KBMSYrIfYJhoAgpvbu5gHR6IKSo1XN/dmWww7Xn2hzcpywqlWSQIXzuD3%2BTxebmiqFSBAA9GhzqjaHhgAgCAB3TA4vEAfQImFOADoEEkktDbqjUQdfo9AZhgUiUeiSJhUV5/kwvLiCCTMODMJCMgpqbT6d9TsQvA49mx6gcAOxWO57HV7ABuqDw6BVCmAEDQDFOe2QCAaACoTcA5hqtbdde69qCQCg4sQEcDHf6jp6COhva5aIdXe6TOqACLfb7u1VMCDO2MWR0QcxmOOYKiC2gEa1EYg5uZRjUJ7W6lMQMLFvXOkD6sReTAkjN62Nxl1ZnMASQYhBLJHLlZ7SdrmHq5oEVpT5n8e1Qzdb3g7GdQlL1bc38b7LFN2bMZg8SQAnqOy6eKxtM5Oazq6yqZ0wl0uV86GKgxRDixmU4ev2p4ALKoHqzwOGOt4Tk%2BHq7huJIvL226Ie2nb3kBHpoXuyGHL2GhRthj5utO9SfqgSRxEwpYERAi5mP4lHfr%2B4qQi62HJseObgZBexMO8OIMOOWHwe66EdihK47nhxHibquFIdJRFiWRwHnAQywMHsdoEAgeAKPJ6lVthjHLlRNF0RscZzpaxbmV%2BfZcbWPGnueV6CWkwAibBanATqkn4TZMlBcZAV7Jp2m6fphnhbqpHYQAfnW6aai5Op4FQewQEFFYKR6R5mjmeYKqgF6idGCXxom8ENuuGEqcZk5qd85IsEkBjksCBAXtRzBsHsAAq9JDXs/DEPiDToCSWA7L11EkoKRAQF6IDnCwEEducVBxIwqxksCI0bNgzL%2BA0Tp7IyeIEEkCggIypxMMgADWW3EFQtCoPilJoCwqJMKiACs6qSJIGgABxSKiEMbJIGwbEDGyJul8FRcQOlrRNU3EOgR1XBAF13q6iV3O1nW0c8wI7F5w30gqSrFlgBgXpgM3Y9NnHwczTCs%2BzJA4%2BgabSVgBZeEW8U6jzfMkhzuMQCNTGfgQa4KCsnbpUxUIHoBNXc5gLNs7LAvTXZVqK8uKt7C2avIBrFgWqcJJvFCRxDXaBNLirPakCqW32wWtBKD2nF6/c8Hk117JHAtjCsM8ABi9LunNRZ9ZghNeEQzpUPyHQMCSSLoBeCufugpy%2B0nStMVQ%2BUmYBBVXUyDPKsAXjTYdjeMlzJnut3KcG7zRty%2BgDqxQomFVcB/e6hbezlyKksejPOr8t54TGvVaD8iK0mcqc3oYuwIB54KwqiuxjjzmmS990y7pJW3HcEDfXf39VU/L%2B/wFZXOO/BQmEK%2B8CCHx5N6U%2BQpSR/glFfS0aY64RRXh6BuvddRIIir/ceJIAC0VxbbITAGAAiewGDi1oJCYgCCAroICigxB38IoeiyjlLBuCTqbUgiSKh08GGMICgvABIYwwgA4RnPSBkJ5sOwPg4mGUm58ICgbYOb8FF8IEdJcRhkcF4PVrfNBvDaFh3oYw0ixiYxGOoUyHsT9cYZnHqQBeLVP530ijOaKWMTa2KOEnE61BZEwkbmcCqjc6EKNTrHTO2dzgKHFgBEKHjJrTWBD47Afi9E/yoKtdWexCHENIbQchZxuEmNRqgvhzCGL%2B2KaolOVpiFrVERAO0Mj0mMKUeyRuqiF4aPwa08x1YykenRjpEes1qhp0WstVAwJwnpwgNE2Jd5UkLKLP4wZpiAqiAIDaCAlI9nVI/nI90%2BliDfVaRsnUpMTLu09NkvJZCKFL05LQP2nDpJnHbM1GqrUyaYA6lHBENN3h7FAm%2BaSi4Tryg%2BcqJgqhDI93dF4NIRhXz1HQLRJgZJ07SVBRRH5JlYWGQgGvYSbMSG%2B2lsPTxeMjgpnRfULF1ErioqYNJXWcY0rOPIkwSkud/iwMLhgEuKYSSkBMEDKwSsgYDIiqE8p2UIA6QIsQjQBy%2BEnO%2BkI70YREJGiWsQNubBBAnjMNEJgxoLo%2BEYFCPyXLLnxjWf02qtwkjEDwLuckIAzJvnpZi2OLLJ4o0ASTO43csDoEZhkPYbcjQdNuJHSmgKDDAtAvSQlChiWWlJegX2oFnRSIEnCoyRxU2QvxfVFgTAwjCxQSRUpEUgkIoUemiAqrzkHi2TsvZlJOVyIuaZeCa1VjEDLEcYEYB1hAzcAwCdS8612sLUSrgjrqoyt1EO30wJx2TunbO/F5jMw6m7qIvY%2BJCAID2Bq/EciUwqmklwVpja5WMJbYkLVIitoMTmCu4CIdO0IF2fsptfCLAAHlQNCCGiSIa2BIMkmwAARQYrJJCvtl3trXfazD77h2jrcNu8Vu71j7o/vo15zwz36SWMWK9N6wUsHva0ltXBfYNM/Swb9rSwMQagzBuDiHkNBV9m2/yq7sIbpHVuo4s6p0zuIyG%2BumoyNoEvKe89l6ECnOvY3W9DHiEPtE8BJ99bm1Ftbb7DjGHrS0S7UB59EVuNwb41BgTLAUMYTQz%2B5BFisPidDIfTdY7pM7rk81eCGZlNUSvJRhA1GNNabo2qPTIUDMLuYxZrz7pHO8dgy5pDbmhN7HQ4ZgdJkJN4YI7JvdCmXHhsjQIaNXhY3YXSyyjMD6HVhZMsMvYwDvTnBSI0UUI6SAZrWT2DgCxaCcCBrwPwHAtCkFQJwfDlhrCeiWCsKmvweCkBAQtybCwEAXCwPENMpAXoJESNNjgkheAsBAOqdUlJJD%2BAhhofwGgNgAE5JAQwht9/wgR5uLeWxwXg91Ej7a0AsOAsAkBimqFnEg5BKAXQUMoQwBshCxfxPN3bf0kh0FohkTH4RaA4%2B%2BiD3ghO6ADGAAoZgd1YsEFILT%2BgxBQNZ0p3jzQvBEfIFuAa%2B64OgiqGqHUfA83eD8EECIMQ7ApAyEEIoFQ6gDukF0CxgwRgUDWGsPoPA0R7qwAGuwYBpBILxG4O9w7S3JTzk4Ng0EBFTBrcsFwdUexsGgY2N7gA6mIF52DISaZOxDzbqw9CgjCGT7HuP8e8DOJgNYu38TECYEkTgPApszbm3zpbnBsDi%2BQMjv0qgIb%2BGwf4SQ0bkDIBYYqBgL1nQQFW5KmwexcCEBIMyDYy6k98%2B/aQY75qBjnZu3d0gD3TyUiRlwb7P3PtAy4BUOGpBqeF9F5DvbQ/c8cA2PnjXYPB8HeH1btIzhJBAA%3D)  
```C++  
#include <iostream>  
#include <cassert>  
#include <stdexcept>  
#include <boost/core/lightweight_test.hpp>  
// #include<boost/core/uncaught_exceptions.hpp>  
  
struct meta {  
    void msg(const char* msg) {  
        std::cerr << msg << std::endl;  
    }  
  
    meta() { msg("Default ctor"); }  
    meta(int v) : value_{v} { msg("Init ctor"); }  
    meta(const meta& o) : value_{o.value_} { msg("Copy ctor"); }  
    meta( meta&& o) noexcept {  
        msg("Move ctor");   
        value_ = o.value_;  
        o.value_ = 0;  
    }  
    meta& operator=(meta&& o) noexcept {  
        msg("Move assign");  
        value_ = o.value_;  
        o.value_ = 0;  
        return *this;  
    }  
    meta& operator=(const meta& o) {   
        msg("Copy assign");  
        value_ = o.value_;  
        return *this;  
    }  
  
    ~meta() {  
        if (value_)  
           msg("Destroy");  
    }  
  
    int value_ = 0;  
};  
  
template<typename T>  
T forward_decltype_auto(std::remove_reference_t<T> &arg) //https://stackoverflow.com/a/57440814/8343353  
{  
    return std::forward<T>(arg);  
}  
  
template <class T>  
struct delayed_forward {  
    delayed_forward() = default;  
    delayed_forward(T&& t) : src_{&t} {}  
    delayed_forward(const T& t) : src_{const_cast<T*>(&t)}, move_{false} {}  
  
    template<typename F>  
    decltype(auto) function_body(T& dst, F&&f)  
    {  
        // struct guard_t  
        // {  
        //     delayed_forward* this_;  
        //     T& dst_;  
        //     unsigned int count_ = boost::core::uncaught_exceptions();  
        //     ~guard_t()  
        //     {  
        //         if(count_ == boost::core::uncaught_exceptions())  
        //         {  
        //             if(this_->src_ != nullptr)  
        //             {  
        //                 if (this_->move_)  
        //                     dst_ = std::move(*this_->src_);  
        //                 else  
        //                     dst_ = *this_->src_;  
        //             }  
        //         }  
        //     }  
        // }guard{this,dst};  
        // return std::forward<F>(f);  
  
        try  
        {  
            decltype(auto)result = std::forward<F>(f);  
            if(src_ != nullptr)  
            {  
                if (move_)  
                    dst = std::move(*src_);  
                else  
                    dst = *src_;  
            }  
            return forward_decltype_auto<decltype(result)>(result);  
        }  
        catch(...)  
        {  
            throw;  
        }  
    }  
  
    T* src_ = nullptr;  
    bool move_ = true;  
};  
  
template <class Meta = meta>  
struct axis {  
    using metadata_type = Meta;  
  
    axis(unsigned n, delayed_forward<metadata_type> meta = {}) {  
        meta.function_body(meta_,[&]  
        {  
            if (n == 0)  
                throw std::invalid_argument("bad argument");  
        });  
    }  
  
private:  
    metadata_type meta_;  
};  
  
// deduction guide  
template <class M>  
axis(unsigned, M) -> axis<M>;  
  
int main() {  
  
    {  
        try {  
            axis(0);  
        } catch(...) {  
        }  
    }  
    std::cerr<<'\n';  
      
    {  
        axis(1);  
    }  
    std::cerr<<'\n';  
  
    {   // move with throw  
        meta m = 1;  
        try {  
            axis(0, std::move(m));  
        } catch(...) {  
            BOOST_TEST_EQ(m.value_, 1);  
        }  
    }  
    std::cerr<<'\n';  
  
    {   // move without throw  
        meta m = 1;  
        axis(1, std::move(m));  
        BOOST_TEST_EQ(m.value_, 0);  
    }  
    std::cerr<<'\n';  
  
    {   // copy with throw  
        meta m = 1;  
        try {  
            axis(0, m);  
        } catch(...) {  
            BOOST_TEST_EQ(m.value_, 1);  
        }  
    }  
    std::cerr<<'\n';  
  
    {   // copy without throw  
        meta m = 1;  
        axis(1, m);  
        BOOST_TEST_EQ(m.value_, 1);  
    }  
    std::cerr<<'\n';  
  
    // deduction guide  
    axis(1, meta{1});  
  
    return boost::report_errors();  
}  
```

---

## Comment 10

> Username: HDembinski  
> Created at: 2023-01-11 10:52:05 UTC  
> Url: https://github.com/boostorg/histogram/issues/379#issuecomment-1378565796  

@jhcarl0814 Just to note, I am currently discussing this topic on the boost-dev mailing list.
