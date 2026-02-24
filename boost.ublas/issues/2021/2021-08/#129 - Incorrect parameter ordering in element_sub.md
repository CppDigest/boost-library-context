# #129 - Incorrect parameter ordering in element_sub [Open]

> Username: isaac868  
> Created at: 2021-08-17 23:32:25 UTC  
> Updated at: 2022-02-13 18:34:54 UTC  
> Url: https://github.com/boostorg/ublas/issues/129  

The parameter ordering of a call to element_wise within one of the element_sub overloads is incorrect.   
  
Since the function signature of element_wise is:  
```  
void element_wise(ublas::matrix<T, L1, opencl::storage> const &a,  
	ublas::matrix<T, L2, opencl::storage> const &b,  
	ublas::matrix<T, L3, opencl::storage> &result,  
	O op, compute::command_queue& queue)  
```  
  
The call in the first element_sub overload should be changed to:  
`element_wise(a, b, result, compute::minus<T>(), queue);`  
as opposed to  
`element_wise(a, b, compute::minus<T>(), result, queue);`

---

## Comment 1

> Username: harjot20022001  
> Created at: 2021-12-30 12:48:09 UTC  
> Url: https://github.com/boostorg/ublas/issues/129#issuecomment-1003014852  

I have submitted a pr that had passed the checks. Can someone review it so it can be merged?

---

## Comment 2

> Username: Neel-Shah-29  
> Created at: 2022-01-09 14:25:08 UTC  
> Url: https://github.com/boostorg/ublas/issues/129#issuecomment-1008307563  

Is there any ongoing effort to solve this issue? If not, I am interested in working on it. Please assign the task to me

---

## Comment 3

> Username: Neel-Shah-29  
> Created at: 2022-02-13 09:13:47 UTC  
> Url: https://github.com/boostorg/ublas/issues/129#issuecomment-1037951141  

Is this issue resolved or yet remaining  to be solved?

---

## Comment 4

> Username: isaac868  
> Created at: 2022-02-13 18:34:54 UTC  
> Url: https://github.com/boostorg/ublas/issues/129#issuecomment-1038327293  

It is not resolved yet. It appears there is are two prq's for this at https://github.com/boostorg/ublas/pull/128 and https://github.com/boostorg/ublas/pull/146#pullrequestreview-881028899. However, they need two people with write access to approve them. The one wihtout all the merge conflicts should probably be the one merged into the develop branch.
