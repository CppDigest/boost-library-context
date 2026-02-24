# #82 - Incorrect behavior of reversed_range with -O2 g++ option [Open]

> Username: mkatliar  
> Created at: 2018-11-22 20:25:29 UTC  
> Updated at: 2019-09-20 15:07:31 UTC  
> Url: https://github.com/boostorg/range/issues/82  

Example code:  
  
```c++  
#include "boost/iterator/counting_iterator.hpp"  
#include "boost/range/iterator_range.hpp"  
#include "boost/range/adaptor/reversed.hpp"  
  
#include <iostream>  
  
int main(int, char **)  
{  
    using iterator = boost::counting_iterator<int>;  
    using iterator_range = boost::iterator_range<iterator>;  
    using reversed_range = boost::reversed_range<iterator_range>;  
  
    iterator first(0), last(3);  
    iterator_range range(first, last);  
    reversed_range r_range = boost::adaptors::reverse(range);  
  
    reversed_range::const_iterator it = r_range.begin();  
    for (; it != r_range.end(); ++it)  
        std::cout << *it << " ";  
    std::cout << std::endl;  
}  
```  
  
Compile with: ` -O2`  
  
Output: 3 integer numbers, different from run to run, similar to  
```  
22007 22007 22007  
```  
(with g++)  
or  
```  
-1807142200 0 0  
```  
(with clang++)  
  
When compiled with `-O0` produces the correct output:  
```  
2 1 0  
```  
  
Reproducible with:  
Boost 1.67.0, 1.68.0  
g++ 6.4.0, 7.3.0, 8.2.0; clang++ 7.0.0-3  
CPU architecture: x86-64 (Intel Xeon)

---

## Comment 1

> Username: mkatliar  
> Created at: 2019-09-11 08:44:49 UTC  
> Url: https://github.com/boostorg/range/issues/82#issuecomment-530284061  

Works correctly with `+O1` in `8.0.0-3`, but not in `g++ 8.3.0`

---

## Comment 2

> Username: morinmorin  
> Created at: 2019-09-20 11:07:21 UTC  
> Url: https://github.com/boostorg/range/issues/82#issuecomment-533510185  

Please don't use `counting_iterator`/`counting_range`. Use `irange(0, 3)`, instead.

---

## Comment 3

> Username: mkatliar  
> Created at: 2019-09-20 12:28:08 UTC  
> Url: https://github.com/boostorg/range/issues/82#issuecomment-533532512  

@morinmorin first, I can't avoid using `counting_iterator`. I am using `boost::make_iterator_range()` with a third-party library, which returns an iterator which can be a `counting_iterator` or some other type, depending on template parameters. Second, as long as the iterator type models the required concept, the code should work.

---

## Comment 4

> Username: neilgroves  
> Created at: 2019-09-20 13:53:09 UTC  
> Url: https://github.com/boostorg/range/issues/82#issuecomment-533562956  

On Fri, 20 Sep 2019 at 13:28, Mikhail Katliar <notifications@github.com>  
wrote:  
  
> @morinmorin <https://github.com/morinmorin> first, I can't avoid using  
> counting_iterator. I am using boost::make_iterator_range() with a  
> third-party library, which returns an iterator which can be a  
> counting_iterator or some other type, depending on template parameters.  
> Second, as long as the iterator type models the required concept, the code  
> should work.  
>  
This isn't morinmorin's defect. It is mine, or at least more mine since I  
wrote the reversed adaptor. The counting_iterator returns a value. This  
isn't a valid model of the (Legacy) random-access iterator concept. Thus it  
isn't totally unreasonable that Boost.Range cannot operate with it in all  
cases.  
  
I may look at this again when I get a little free time. In the interim you  
can have a full refund. Please don't complain to morinmorin who is helping  
you and I, often clearing up my mistakes and my frequent inability to spare  
as much time as I would like. Patches are always welcome.  
  
Regards,  
Neil Groves  
  
  
  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/range/issues/82?email_source=notifications&email_token=ABTIJ2RHSEHDO6CJEJ4ACGLQKS6VTA5CNFSM4GF7OQW2YY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOD7GQ6YA#issuecomment-533532512>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ABTIJ2R3IE2743C7J53Z5VTQKS6VTANCNFSM4GF7OQWQ>  
> .  
>

---

## Comment 5

> Username: morinmorin  
> Created at: 2019-09-20 15:07:31 UTC  
> Url: https://github.com/boostorg/range/issues/82#issuecomment-533592239  

@mkatliar  
If you want to use them with `reversed` adaptor (among others), the option would be convert them into `integer_range`, which is defined in `irange.hpp`.  
  
`counting_iterator` is a beast; deferencing it returns a reference to the data it owns. It cannot work even with `std::reverse_iterator`. Unfortunately, changing its `reference` type to a value type would not be an option now, since that would demote the iterator category to InputIterator (and then break some users' code).  
  
@neilgroves  
Hey, it's just that `counting_iterator` is a beast 😄   
  
Regards,  
Michel Morin
