# #788 - Add test for extensions/test/algorithms/parse.hpp [Closed]

> Username: mloskot  
> Created at: 2020-12-23 09:34:28 UTC  
> Updated at: 2021-02-15 18:19:13 UTC  
> Closed at: 2021-02-15 08:23:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/788  

It would be helpful to have a test covering features in the [extensions/test/algorithms/parse.hpp](https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/extensions/algorithms/parse.hpp) extension, see https://github.com/boostorg/geometry/pull/786

---

## Comment 1

> Username: GauravSarkar  
> Created at: 2020-12-23 12:15:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/788#issuecomment-750243939  

@mloskot  if you can give a little bit more details on this issue, I was willing to take up the issue.

---

## Comment 2

> Username: GauravSarkar  
> Created at: 2020-12-23 13:37:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/788#issuecomment-750303835  

As far as I have seen there is no parse.hpp under  ```extensions/test/algorithms```. So are you asking to create it and add test results of features as per #786 ?

---

## Comment 3

> Username: mloskot  
> Created at: 2020-12-23 21:11:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/788#issuecomment-750475819  

The file is there, see the link in the issue description that I've just updated.  
  
I don't really have anything to add.  
It's a typical procedure: read the code, learn what's the public interface, add test cases and i exercise functions of the public interface.

---

## Comment 4

> Username: GauravSarkar  
> Created at: 2020-12-24 08:35:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/788#issuecomment-750806502  

Thanks @mloskot  I got it. I  will go through the code

---

## Comment 5

> Username: ayushgupta138  
> Created at: 2020-12-29 12:06:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/788#issuecomment-752052803  

@mloskot  could you please provide some more details regarding the issue and about the test cases that needs to added. I am willing to work on this issue, since there hasn't been a pull request lately.

---

## Comment 6

> Username: mloskot  
> Created at: 2020-12-29 12:38:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/788#issuecomment-752061043  

@ayushgupta138 See my earlier comment.

---

## Comment 7

> Username: RajWorking  
> Created at: 2021-01-01 06:18:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/788#issuecomment-753268806  

You want us to create X?  
include/boost/geometry/extensions/algorithms/distance_info.hpp : extensions/test/algorithms/distance_info.cpp  
include/boost/geometry/extensions/algorithms/parse.hpp : X  
X is extensions/test/algorithms/parse.cpp, am I correct?

---

## Comment 8

> Username: mloskot  
> Created at: 2021-01-01 10:20:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/788#issuecomment-753297365  

I think, you've decoded the file structure quite right.

---

## Comment 9

> Username: ayushgupta138  
> Created at: 2021-01-02 14:59:04 UTC  
> Updated at: 2021-02-14 09:26:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/788#issuecomment-753483434  

(Assuming parse.hpp is included)  
```  
typedef bg::model::point<double, 2, bg::cs::spherical_equatorial<radian>> earth;  
earth ear;  
parse<earth>(ear, "17.65R N", "23.2R W");  
cout << get<0>(ear) << " " << get<1>(ear) << "\n";  
```  
The above code snippet outputs `-23.2 17.65`  
but if space is added between the value and 'R'   
```  
parse<earth>(ear, "17.65 R N", "23.2 R W");  
```  
the values 17.65 and 23.2 are assumed as degree values and converted to radian outputting    
```  
-0.404916  0.308051  
```  
Since N/E/W/S are working properly even if spaces are added,  
@mloskot  is this a bug?  
A clarification is needed for the same.

---

## Comment 10

> Username: ayushgupta138  
> Created at: 2021-01-06 13:59:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/788#issuecomment-755313998  

@mloskot could you please clarify the above query.

---

## Comment 11

> Username: mloskot  
> Created at: 2021-01-07 09:45:00 UTC  
> Updated at: 2021-01-07 09:45:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/788#issuecomment-756006193  

@ayushgupta138  I'm travelling until next week, mostly offline, so I can't check myself.  
It's possible, especially that we are not having proper test coverage for that stuff, there is a bug. So, some code reading and debugging is needed to confirm.

---

## Comment 12

> Username: mloskot  
> Created at: 2021-02-14 12:39:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/788#issuecomment-778772206  

Regarding question in https://github.com/boostorg/geometry/issues/788#issuecomment-753483434 it looks like these:  
- `<number>`  
- `<number><unit>`   
  
are all treated as a single token by design and any following white-space triggers processing of the value:  
  
https://github.com/boostorg/geometry/blob/e710403b4d220ccf1c07c7035f36d1e86d6be75e/include/boost/geometry/srs/projections/impl/dms_parser.hpp#L230-L234  
  
Any other tokens that follow are processed as value-less.   
So, I think there is no bug, if anything, it's just limited flexibility (possible to improve, of course).

---

## Comment 13

> Username: ayushgupta138  
> Created at: 2021-02-14 12:55:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/788#issuecomment-778774144  

I should have been careful about the term 'bug' in my comment. After going through the code of dms_parser.hpp, the details were clear to me(Thanks for the confirmation though). Assuming the same, I have submitted a pr for the unit test. It would really be helpful if you could provide some suggestions on it for further improvement.

---

## Comment 14

> Username: mloskot  
> Created at: 2021-02-15 18:18:19 UTC  
> Updated at: 2021-02-15 18:19:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/788#issuecomment-779384931  

Initial implementation of the tests have been added in #802  
  
Further improvements added in #806
