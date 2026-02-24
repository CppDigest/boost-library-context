# #843 - Non-throwing conversions for Eigen::Matrix4d [Closed]

> Username: nigels-com  
> Created at: 2023-01-20 01:05:35 UTC  
> Updated at: 2023-02-27 23:27:23 UTC  
> Closed at: 2023-02-27 23:27:23 UTC  
> Url: https://github.com/boostorg/json/issues/843  

Boost 1.81.0  
Visual C++ 2019 (64-bit)  
  
I'm trying to follow the [documentation](https://www.boost.org/doc/libs/1_81_0/libs/json/doc/html/json/conversion/non_throwing_conversions.html) for using the `tag_invoke` method of adding support for [Eigen](https://eigen.tuxfamily.org/index.php?title=Main_Page) dense matrix types.  
  
I've managed to get things working including appropriate error checking, but lacking confidence that I'm doing it the intended or correct way.  The `ip_address` example discussed in the documentation uses: `return make_error_code( std::errc::invalid_argument );` which is resulting in a compilation error for me:  
  
```  
XXX\EigenJson.cpp(25,62): error C2440: 'return': cannot convert from 'std::error_code' to 'boost::system::result<Eigen::Matrix<double,4,4,0,4,4>,boost::system::error_code>' [XXXX\xxx.vcxproj]  
```  
  
There isn't much documentation of `boost::json::result_for` (or relevant links) but `result_from_errno` does seem to work.  
  
In particular it seems unfortunate to collect the `error_code ec` for the `to_number<double>()` and not have a way to set that error as the result.  
  
Is the documentation just a bit stale w.r.t boost 1.81.0?  Am I missing something obvious?  Tell me I'm doing it wrong. :)  
  
```  
result_for<Eigen::Matrix4d, value>::type  
tag_invoke(const try_value_to_tag<Eigen::Matrix4d>&, const value& jv)  
{  
    if (!jv.is_array())  
    {  
        BOOST_STATIC_CONSTEXPR boost::source_location loc = BOOST_CURRENT_LOCATION;  
        return result_from_errno<Eigen::Matrix4d>(EINVAL, &loc);  
    }  
  
    constexpr size_t size = 16;  
    const auto& a = jv.as_array();  
  
    if (a.size() != size)  
    {  
        BOOST_STATIC_CONSTEXPR boost::source_location loc = BOOST_CURRENT_LOCATION;  
        return result_from_errno<Eigen::Matrix4d>(EINVAL, &loc);  
    }  
  
    Eigen::Matrix4d v;  
    for (size_t i = 0; i < size; ++i)  
    {  
        error_code ec;  
        v.data()[i] = a[i].to_number<double>(ec);  
        if (ec.failed())  
        {  
            BOOST_STATIC_CONSTEXPR boost::source_location loc = BOOST_CURRENT_LOCATION;  
            return result_from_errno<Eigen::Matrix4d>(EINVAL, &loc);  
        }  
    }  
    return v;  
}  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2023-01-20 01:10:19 UTC  
> Url: https://github.com/boostorg/json/issues/843#issuecomment-1397806963  

> In particular it seems unfortunate to collect the `error_code ec` for the `to_number<double>()` and not have a way to set that error as the result.  
  
`if( ec.failed() ) return ec;` should work.

---

## Comment 2

> Username: pdimov  
> Created at: 2023-01-20 01:14:38 UTC  
> Url: https://github.com/boostorg/json/issues/843#issuecomment-1397809645  

`return make_error_code( std::errc::invalid_argument );` also works for me: https://godbolt.org/z/cbTPcdefs

---

## Comment 3

> Username: pdimov  
> Created at: 2023-01-20 01:23:34 UTC  
> Url: https://github.com/boostorg/json/issues/843#issuecomment-1397814579  

It doesn't seem to work when I use the actual Eigen matrix, though. https://godbolt.org/z/9n9xY33Pn :-)  
  
I'll open an issue in Boost.System about that. The reason for it not working is that the Eigen matrix has a constructor taking any `T`, so `result` considers the conversion ambiguous, but this constructor is explicit, so it shouldn't be in play for implicit conversions.

---

## Comment 4

> Username: nigels-com  
> Created at: 2023-01-20 01:25:21 UTC  
> Url: https://github.com/boostorg/json/issues/843#issuecomment-1397815495  

Indeed, Eigen does tend to be complex to deal with.  
  
```  
    for (size_t i = 0; i < size; ++i)  
    {  
        error_code ec;  
        v.data()[i] = a[i].to_number<double>(ec);  
        if (ec.failed())  
        {  
            return ec;  
        }  
    }  
  
XXX\EigenJson.cpp(45,22): error C2440: 'return': cannot c  
onvert from 'boost::json::error_code' to 'boost::system::result<Eigen::Matrix<double,4,4,0,4,4>,boost::system::error_co  
de>' [XXXX\spatial_math.vcxproj]  
```

---

## Comment 5

> Username: pdimov  
> Created at: 2023-01-20 01:31:39 UTC  
> Url: https://github.com/boostorg/json/issues/843#issuecomment-1397818637  

This is a manifestation of the same issue. It will be fixed in the next release, but in the meantime, you can work around it by explicitly requesting error construction of the result by replacing `return ec;` with `return { boost::system::in_place_error, ec };`  
  
This also works for the `make_error_code` case: https://godbolt.org/z/xqjv849Gs

---

## Comment 6

> Username: nigels-com  
> Created at: 2023-01-20 01:54:44 UTC  
> Url: https://github.com/boostorg/json/issues/843#issuecomment-1397830737  

Thanks for the workaround, appreciated.  
  
```  
result_for<Eigen::Matrix4d, value>::type  
tag_invoke(const try_value_to_tag<Eigen::Matrix4d>&, const value& jv)  
{  
    const auto a = jv.if_array();  
    if (!a)  
    {  
        return { boost::system::in_place_error, make_error_code(boost::system::errc::invalid_argument) };  
    }  
  
    constexpr size_t size = 16;  
    if (a->size() != size)  
    {  
        return { boost::system::in_place_error, make_error_code(boost::system::errc::invalid_argument) };  
    }  
  
    Eigen::Matrix4d v;  
    for (size_t i = 0; i < size; ++i)  
    {  
        error_code ec;  
        v.data()[i] = (*a)[i].to_number<double>(ec);  
        if (ec.failed())  
        {  
            return { boost::system::in_place_error, ec };  
        }  
    }  
    return v;  
}  
```

---

## Comment 7

> Username: pdimov  
> Created at: 2023-01-20 02:23:11 UTC  
> Url: https://github.com/boostorg/json/issues/843#issuecomment-1397847993  

You are welcome.  
  
Note that your previous  
```  
        BOOST_STATIC_CONSTEXPR boost::source_location loc = BOOST_CURRENT_LOCATION;  
        return result_from_errno<Eigen::Matrix4d>(EINVAL, &loc);  
```  
was actually better, because it added a source location to the returned error code. If you later print `r.error().what()`, or invoke `r.value()` which will throw `system_error`, the message in this case will contain the source file/line of the `BOOST_CURRENT_LOCATION`. This is convenient if you want to know what exactly failed, because `invalid_argument` by itself doesn't say much.  
  
See e.g. https://godbolt.org/z/76a631Pfo.  
  
You're not required to use `invalid_argument` either; you can return e.g. `boost::json::error::not_array` in the first check, and `boost::json::error::size_mismatch` in the second: https://godbolt.org/z/1eh9W8eW3

---

## Comment 8

> Username: pdimov  
> Created at: 2023-01-20 02:31:53 UTC  
> Url: https://github.com/boostorg/json/issues/843#issuecomment-1397852876  

(And if you want both custom error codes and source locations, https://godbolt.org/z/vf38enaPd.)

---

## Comment 9

> Username: nigels-com  
> Created at: 2023-01-25 07:05:02 UTC  
> Url: https://github.com/boostorg/json/issues/843#issuecomment-1403180029  

Happy to close this issue now, thanks for the advice.

---

## Comment 10

> Username: grisumbras  
> Created at: 2023-02-27 13:50:36 UTC  
> Url: https://github.com/boostorg/json/issues/843#issuecomment-1446356580  

Wait, this issue should be closed now, right @nigels-com ?

---

## Comment 11

> Username: nigels-com  
> Created at: 2023-02-27 21:20:18 UTC  
> Url: https://github.com/boostorg/json/issues/843#issuecomment-1447107246  

Yes, I think so.  Thanks.
