# #155 - Testing the main() function [Closed]

> Username: pinventado  
> Created at: 2018-08-31 14:29:26 UTC  
> Updated at: 2019-11-12 08:06:01 UTC  
> Closed at: 2019-11-12 08:06:01 UTC  
> Url: https://github.com/boostorg/test/issues/155  

I'm trying to adopt a test-driven approach for my introductory programming class where our first topics are on variable declarations, using cout, simple conditions, and loops. Students don't know how to use functions yet, so I was wondering if it was possible to run a unit test on main.  
  
I've seen a couple of discussions that suggest ways to grab data from cout ([see accepted answer](https://stackoverflow.com/questions/5405016/can-i-check-my-programs-output-with-boost-test)) that then allow testing, but the issue is that students' programs never really call functions. I don't know how to call the main function from the unit test, if that's even possible. Here's sample code of the types of programs students make to give some context.  
  
```cpp  
#include <iostream>  
using namespace std;  
  
int main()  
{  
  int num1;  
  int num2;  
  cout << "Please enter the first number: ";  
  cin >> num1;  
  cout << "Please enter the second number: ";  
  cin >> num2;  
  cout << "The sum of " << num1 << " and " << num2 << " is " << num1 + num2 << endl;  
  return 0;  
}  
```

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-09-02 18:20:43 UTC  
> Url: https://github.com/boostorg/test/issues/155#issuecomment-417949302  

I am not sure to understand what is needed here, let me know if this discussion is going to the expected direction.  
  
It is possible to write code in the `main` with boost.test, see for example this documentation entry: https://www.boost.org/doc/libs/1_68_0/libs/test/doc/html/boost_test/adv_scenarios/single_header_customizations/entry_point.html  
  
It is also possible to test for streams:  
https://www.boost.org/doc/libs/1_68_0/libs/test/doc/html/boost_test/testing_tools/output_stream_testing.html  
  
However, I do not think this is what you want. If you want a test-driven approach, and still being very introductory, I am tempted to think that using `assert` would be something that may be easily understood at the beginning. Then of course, you may develop by pointing out the shortcomings of the `assert` (debug only, aborts the program, etc) and introduce unit testing frameworks this way.

---

## Comment 2

> Username: pinventado  
> Created at: 2018-09-02 18:53:38 UTC  
> Url: https://github.com/boostorg/test/issues/155#issuecomment-417951962  

Your first suggestions makes a lot of sense, but I hesitate to introduce students to unit testing right away. It might be too much for them to process and it's not really the focus of the course.  
  
Your second suggestion seems more plausible because `assert` shouldn't be too difficult to understand and is a good introduction to unit tests.  
  
Aside from using `assert`, I'm still searching for other alternatives to do unit testing without getting students to create functions. I'm thinking that students can run a script or `make` that runs the unit test without them having to add anything outside of our usual discussions to their code.

---

## Comment 3

> Username: tiendq  
> Created at: 2018-10-12 00:57:21 UTC  
> Url: https://github.com/boostorg/test/issues/155#issuecomment-429169037  

Why don't just move code inside `main` into normal functions and unit tested them as usual?

---

## Comment 4

> Username: pinventado  
> Created at: 2018-10-12 03:48:59 UTC  
> Url: https://github.com/boostorg/test/issues/155#issuecomment-429195581  

I'm teaching an introductory programming course and want to use unit tests to help students get immediate feedback. The way it's taught is that functions are introduced later. In that case, I won't be able to do unit testing until then.  
  
So, I'm trying to see if I can get that effect of unit testing without the extra requirement of having to use functions yet.

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2019-11-12 08:06:00 UTC  
> Url: https://github.com/boostorg/test/issues/155#issuecomment-552780371  

@pinventado I am closing the issue. If you need more help, please open a new ticket.   
Thanks!
