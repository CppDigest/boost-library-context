# #957 - Typo in students T example code [Closed]

> Username: JohnBarrabas  
> Created at: 2023-02-23 04:13:48 UTC  
> Updated at: 2023-02-23 09:53:42 UTC  
> Closed at: 2023-02-23 09:53:42 UTC  
> Url: https://github.com/boostorg/math/issues/957  

Boost 1.71.0  
  
In the file https://www.boost.org/doc/libs/1_43_0/libs/math/example/students_t_two_samples.cpp  
  
  
`  
cout << setw(55) << left << "Pooled Standard Deviation" << "=  " << v << "\n";  
`  
  
should be  
  
`  
cout << setw(55) << left << "Pooled Standard Deviation" << "=  " << sp << "\n";  
`  
  
The "v" variable is degrees of freedom, the "sp" variable is the standard deviation.  
  
The [boost documentation page](https://www.boost.org/doc/libs/1_43_0/libs/math/doc/sf_and_dist/html/math_toolkit/dist/stat_tut/weg/st_eg/two_sample_students_t.html) describing this file also has the error embedded in the text:  
  
`  
   Pooled Standard Deviation  =  326.00000     <-- Incorrect number shown in html page describing sample code  
`

---

## Comment 1

> Username: WarrenWeckesser  
> Created at: 2023-02-23 06:19:22 UTC  
> Url: https://github.com/boostorg/math/issues/957#issuecomment-1441262553  

You have linked to an old version (1.43.0) of the example.  That mistake was [fixed 10 years ago](https://github.com/boostorg/math/commit/1211184b202d112d8cd4dea6a322da0686184c4c).  Take a look at the latest released version (1.81.0): https://www.boost.org/doc/libs/1_81_0/libs/math/example/students_t_two_samples.cpp

---

## Comment 2

> Username: jzmaddock  
> Created at: 2023-02-23 09:53:42 UTC  
> Url: https://github.com/boostorg/math/issues/957#issuecomment-1441471588  

Closing as obsolete, please re-open if I've missed something.
