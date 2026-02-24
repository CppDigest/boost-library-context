# #986 - Add support to universal tag_invoke for class as struct with macro BOOST_DESCRIBE_CLASS [Closed]

> Username: marc-viala  
> Created at: 2024-02-28 17:25:22 UTC  
> Updated at: 2024-03-02 20:34:46 UTC  
> Closed at: 2024-03-02 08:38:17 UTC  
> Url: https://github.com/boostorg/json/issues/986  

### Version of Boost: 1.84  
  
### Steps necessary to reproduce the problem  
  
It is not possible w/ macro BOOST_DESCRIBE_CLASS to perform automatic jsonify as we can w/ a plain structure. The following code doesn't compile for example.  
  
```  
#define BOOST_TEST_MODULE inprogress_test_suite  
#include <boost/test/unit_test.hpp>  
  
#include <boost/describe.hpp>  
#include <boost/json.hpp>  
  
namespace bjs = boost::json  
  
namespace app {  
  class a_class {  
  public:  
    a_class(const int a = {}, const int b = {}): a_{a}, b_{b}  
    {}  
  
    bool operator==(const a_class& rhs) const  
    { return a_ == rhs.a_ && b_ == rhs.b_; }  
  
  private:  
    int a_;  
    int b_;  
  
    BOOST_DESCRIBE_CLASS(a_class, (), (operator==), (), (a_, b_))  
  };   
}  
  
BOOST_AUTO_TEST_CASE(test_bjson_class)  
{  
  app::a_class a{100, 200};  
  const auto jv{bjs::value_from(a)};  
}  
```  
  
### All relevant compiler information  
Microsoft (R) C/C++ version 19.16.27051

---

## Comment 1

> Username: pdimov  
> Created at: 2024-02-28 17:42:59 UTC  
> Url: https://github.com/boostorg/json/issues/986#issuecomment-1969514528  

By default, Boost.JSON doesn't work with described classes if they have private members, because it doesn't know whether there's a class invariant covering them.  
  
But you can override this by adding the following line:  
  
```  
template<> struct boost::json::is_described_class<app::a_class>: std::true_type {};  
```  
  
See https://godbolt.org/z/8P4zYeMnK.

---

## Comment 2

> Username: pdimov  
> Created at: 2024-02-28 17:45:01 UTC  
> Url: https://github.com/boostorg/json/issues/986#issuecomment-1969517846  

Also see https://www.boost.org/doc/libs/1_84_0/libs/json/doc/html/json/ref/boost__json__is_described_class.html

---

## Comment 3

> Username: marc-viala  
> Created at: 2024-02-28 21:57:44 UTC  
> Url: https://github.com/boostorg/json/issues/986#issuecomment-1969988525  

Hi Peter,  
  
Sorry for my late reply. I take note of your explanation.  
  
Thank you again for your implication.  
  
Best regards,  
  
Marc  
  
Le mer. 28 févr. 2024 à 18:45, Peter Dimov ***@***.***> a  
écrit :  
  
> Also see  
> https://www.boost.org/doc/libs/1_84_0/libs/json/doc/html/json/ref/boost__json__is_described_class.html  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/json/issues/986#issuecomment-1969517846>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AVCM4VAJXNOJSVB7XHSWE43YV5UKRAVCNFSM6AAAAABD6OAXWCVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMYTSNRZGUYTOOBUGY>  
> .  
> You are receiving this because you authored the thread.Message ID:  
> ***@***.***>  
>  
  
  
--   
Marc Viala

---

## Comment 4

> Username: grisumbras  
> Created at: 2024-02-29 13:34:38 UTC  
> Url: https://github.com/boostorg/json/issues/986#issuecomment-1971158466  

While Peter's suggestion would make the example compile, it won't actually solve the problem, as we obviously can't access private members. This can only be solved by a custom tag_invoke implementation.  
  
Alternatively, it could be done if we implement delegating serialisation to another type, as we've discussed in Slack.

---

## Comment 5

> Username: pdimov  
> Created at: 2024-02-29 13:39:27 UTC  
> Url: https://github.com/boostorg/json/issues/986#issuecomment-1971166414  

Reflected private members can be accessed. It should work.

---

## Comment 6

> Username: pdimov  
> Created at: 2024-02-29 13:44:37 UTC  
> Url: https://github.com/boostorg/json/issues/986#issuecomment-1971176338  

Oh, it doesn't, because Boost.JSON doesn't enumerate the private members. (https://godbolt.org/z/znYGds9zj)  
  
`mod_public` here  
  
https://github.com/boostorg/json/blob/8f5b63510b07caa96f413777a412d930781fac5d/include/boost/json/detail/value_from.hpp#L159  
  
needs to be changed to `mod_any_access`.

---

## Comment 7

> Username: grisumbras  
> Created at: 2024-02-29 13:46:29 UTC  
> Url: https://github.com/boostorg/json/issues/986#issuecomment-1971181103  

Oh, I didn't know that it would work for private members.

---

## Comment 8

> Username: pdimov  
> Created at: 2024-02-29 13:50:46 UTC  
> Url: https://github.com/boostorg/json/issues/986#issuecomment-1971188650  

https://godbolt.org/z/8PEaccz1d

---

## Comment 9

> Username: marc-viala  
> Created at: 2024-03-01 06:15:19 UTC  
> Url: https://github.com/boostorg/json/issues/986#issuecomment-1972578683  

Hello Peter,  
  
I don't check my email on a regular basis. Sorry for the delay. Yesterday,  
when I test your first fix (inserting this line template<> struct  
boost::json::is_described_class<app::a_class>: std::true_type {};). I have  
observed that compilation was fine but no member was serialized (as you  
have explained). At this point, I have tried without success to adapt your  
helpers functions in your documentation (sections "automatic JSON"). And  
now I saw your emails.  
  
Do you to think that the modification from mod_public to mod_any_access (  
https://github.com/boostorg/json/blob/8f5b63510b07caa96f413777a412d930781fac5d/include/boost/json/detail/value_from.hpp#L159)  
will be merge at some point? Or it needs some careful analysis?  
  
Best regards,  
  
Marc  
  
Le jeu. 29 févr. 2024 à 14:50, Peter Dimov ***@***.***> a  
écrit :  
  
> https://godbolt.org/z/8PEaccz1d  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/json/issues/986#issuecomment-1971188650>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AVCM4VHS3DYCFWB2ZFQXUWTYV4Y4FAVCNFSM6AAAAABD6OAXWCVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMYTSNZRGE4DQNRVGA>  
> .  
> You are receiving this because you authored the thread.Message ID:  
> ***@***.***>  
>  
  
  
--   
Marc Viala

---

## Comment 10

> Username: marc-viala  
> Created at: 2024-03-02 20:34:45 UTC  
> Url: https://github.com/boostorg/json/issues/986#issuecomment-1974902324  

Thank your very much for the correction of this issue and within a very short time-frame.
