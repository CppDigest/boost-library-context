# #30 unpack types from mpl::joint_view for make_variant_over function (tra… [Merged]

> Username: very-cool-name  
> Created at: 2017-03-15 18:20:26 UTC  
> Updated at: 2017-04-25 16:45:13 UTC  
> Merged at: 2017-04-22 06:04:10 UTC  
> Closed at: 2017-04-22 06:04:10 UTC  
> Url: https://github.com/boostorg/variant/pull/30  

…c #8554)

---

## Comment 1

> Username: apolukhin  
> Created_at: 2017-03-18 09:19:03 UTC  
> Url: https://github.com/boostorg/variant/pull/30#issuecomment-287528647  

Looks like the solution could be more generic.  
  
There's a lot of Forward Sequence containers and views:  
* http://www.boost.org/doc/libs/1_55_0/libs/mpl/doc/refmanual/filter-view.html  
* ? http://www.boost.org/doc/libs/1_55_0/libs/mpl/doc/refmanual/iterator-range.html  
* http://www.boost.org/doc/libs/1_55_0/libs/mpl/doc/refmanual/joint-view.html  
* http://www.boost.org/doc/libs/1_55_0/libs/mpl/doc/refmanual/transform-view.html  
* http://www.boost.org/doc/libs/1_55_0/libs/mpl/doc/refmanual/zip-view.html  
  
  
Just always copy instead of specializing for joint_views. Something like this:  
  
```cpp  
template <typename Types>  
struct make_variant_over  
{  
private: // precondition assertions  
  
    BOOST_STATIC_ASSERT(( ::boost::mpl::is_sequence<Types>::value ));  
    typedef typename mpl::copy<Types, boost::mpl::back_inserter<mpl::vector<> > >::type copied_sequence_t;  
  
public: // metafunction result  
  
    typedef variant<  
          detail::variant::over_sequence<copied_sequence_t>  
        > type;  
  
};  
  
#ifndef BOOST_VARIANT_DO_NOT_USE_VARIADIC_TEMPLATES  
  
// Optimizing compilation speed by avoiding MPL computations for the most common case  
template <typename... Types>  
struct make_variant_over< mpl::vector<Types...> >  
{  
private: // precondition assertions  
    typedef mpl::vector<Types...> sequence_t;  
  
public: // metafunction result  
    typedef variant<  
          detail::variant::over_sequence<sequence_t>  
        > type;  
  
};  
  
#endif // #ifndef BOOST_VARIANT_DO_NOT_USE_VARIADIC_TEMPLATES  
```

---

## Comment 2

> Username: apolukhin  
> Created_at: 2017-04-22 23:08:41 UTC  
> Updated_at: 2017-04-22 23:09:03 UTC  
> Url: https://github.com/boostorg/variant/pull/30#issuecomment-296407416  

Something's wrong: https://travis-ci.org/apolukhin/variant/jobs/224730107

---

## Comment 3

> Username: very-cool-name  
> Created_at: 2017-04-23 08:36:22 UTC  
> Url: https://github.com/boostorg/variant/pull/30#issuecomment-296428150  

Окей, посмотрю сегодня.  
  
С уважением,  
Михаил  
  
> 23 апр. 2017 г., в 2:08, Antony Polukhin <notifications@github.com> написал(а):  
>   
> Что-то не зашло: https://travis-ci.org/apolukhin/variant/jobs/224730107  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub, or mute the thread.  
>

---

## Comment 4

> Username: very-cool-name  
> Created_at: 2017-04-25 16:45:12 UTC  
> Url: https://github.com/boostorg/variant/pull/30#issuecomment-297091536  

Хм, там чего-то непонятное:  
  
есть метафункция *make_recursive_variant*, которая возвращает  
*vec3_t:* boost::variant<  
boost::detail::variant::recursive_flag<  
boost::detail::variant::over_sequence< boost:mpl::vector<...> > > >,  
а есть функция *make_variant_over*, которая возвращает *vec4_t:*  
boost::variant< boost::detail::variant::over_sequence<  
boost::mpl::v_item<...>,  
boost::mpl::v_item<...> >  
  
и получается, что компилятор ругается на  
*std::vector<vec3_t>::push_back(vec4_t)*.  
  
Наоборот, кстати, не ругается. Я не очень понимаю откуда *make_variant_over  
*берёт эти v_item. Что можно посмотреть?  
  
У меня до конца недели не будет скорее всего доступа к компьютеру. Но я на  
следующей тогда продолжу починку.  
  
С уважением,  
Михаил  
  
  
2017-04-23 11:36 GMT+03:00 Михаил Максимов <vigorous.activity@gmail.com>:  
  
> Окей, посмотрю сегодня.  
>  
> С уважением,  
> Михаил  
>  
> 23 апр. 2017 г., в 2:08, Antony Polukhin <notifications@github.com>  
> написал(а):  
>  
> Что-то не зашло: https://travis-ci.org/apolukhin/variant/jobs/224730107  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/variant/pull/30#issuecomment-296407416>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ADe8_0Q-FR5ghT557FyfUPkgkibKEi-8ks5ryoh5gaJpZM4MeVw0>  
> .  
>  
>

---
