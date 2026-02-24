# #132 - Empty array appearing on Linux but not Windows [Closed]

> Username: AlexN12  
> Created at: 2020-08-03 14:37:30 UTC  
> Updated at: 2020-08-04 14:43:43 UTC  
> Closed at: 2020-08-04 14:43:43 UTC  
> Url: https://github.com/boostorg/json/issues/132  

Hello,  
  
I'm currently using this library to create JSON files. Those are graph files so they have the fields "nodes" and "vertices", that both contain an array of elements. However after using this library to generate those JSON files, both those fields are arrays, but then the first element of this array is an empty array and is not present with the Windows version, that I would really like to remove. I was wondering if anyone knew where this could come from and how to solve it.  
  
Edit: I solved this by using CLang to compile instead of gcc  
  
Thank you very much.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-08-03 15:41:54 UTC  
> Updated at: 2020-08-03 15:42:06 UTC  
> Url: https://github.com/boostorg/json/issues/132#issuecomment-668093889  

Well this sounds like a bug, all compilers should produce the same result! Can you please provide a small program which replicates the defect?

---

## Comment 2

> Username: AlexN12  
> Created at: 2020-08-04 10:11:30 UTC  
> Url: https://github.com/boostorg/json/issues/132#issuecomment-668509447  

I managed to isolate the problem into a minimal code, you'll see that the difference of initialization between data_a and data_b causes only data_b to contain the empty array (when compiling with gcc).  
  
```c++  
int main()  
{  
	auto data_a = array();  
	auto data_b {array()};  
  
	data_a.emplace_back(object({{"foo", "bar"}}));  
	data_b.emplace_back(object({{"foo", "bar"}}));  
      
	auto radix {object({{"data_a", data_a}, {"data_b", data_b}})};  
  
	std::cout << radix << std::endl;  
  
	return 0;  
}  
```

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-08-04 10:29:19 UTC  
> Url: https://github.com/boostorg/json/issues/132#issuecomment-668516906  

I've always found that brace/initializer_list initialisation is a lottery in c++.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-08-04 13:51:02 UTC  
> Url: https://github.com/boostorg/json/issues/132#issuecomment-668608472  

Ahhh..... Well, sorry about that but this is a problem with initializer lists in C++ and there is not much that can be done about it. Different compilers sometimes behave differently as you have discovered. You should never explicitly create a variable of type `std::inititalizer_list` except as a function parameter. You are doing so here:  
```  
	auto data_b {array()};  
```  
  
`data_b` has type `std::initializer_list` and is not a function parameter. This will create headaches.

---

## Comment 5

> Username: AlexN12  
> Created at: 2020-08-04 14:43:43 UTC  
> Url: https://github.com/boostorg/json/issues/132#issuecomment-668638944  

I understand, thank you very much for these precisions!
