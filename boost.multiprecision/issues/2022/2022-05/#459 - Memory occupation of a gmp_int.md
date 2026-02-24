# #459 - Memory occupation of a gmp_int? [Closed]

> Username: NAThompson  
> Created at: 2022-05-10 02:23:32 UTC  
> Updated at: 2023-02-16 17:04:15 UTC  
> Closed at: 2023-02-16 17:04:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/459  

Is there an easy way to query how much memory a `boost::multiprecision::mpz_int` occupies? I see that the boost wrapper occupies 128 bits, but it's unclear how to get the memory occupation of the underlying data.  
  
(Perhaps a question for `mpz` I know . . .)

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-05-10 16:23:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/459#issuecomment-1122613947  

It is a GMP question, but a quick google doesn't reveal anything much.  
  
You could peek inside the mpz_t structure:  
  
```  
std::size_t get_capacity(const boost::multiprecision::mpz_int& i)  
{  
   return i.backend().data()[0]._mp_alloc * sizeof(i.backend().data()[0]._mp_d[0]);  
}  
std::size_t get_used(const boost::multiprecision::mpz_int& i)  
{  
   return i.backend().data()[0]._mp_size * sizeof(i.backend().data()[0]._mp_d[0]);  
}  
  
int main()  
{  
   boost::multiprecision::mpz_int i(34);  
   std::cout << get_capacity(i) << std::endl;  
   std::cout << get_used(i) << std::endl;  
  
   i <<= 100000;  
   std::cout << get_capacity(i) << std::endl;  
   std::cout << get_used(i) << std::endl;  
  
   i >>= 10000;  
   std::cout << get_capacity(i) << std::endl;  
   std::cout << get_used(i) << std::endl;  
  
   return 0;  
}  
```  
  
Prints:  
  
```  
8  
8  
12512  
12504  
12512  
11256  
```

---

## Comment 2

> Username: NAThompson  
> Created at: 2022-05-11 02:38:20 UTC  
> Updated at: 2022-05-11 02:44:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/459#issuecomment-1123117195  

@jzmaddock : gracias, that works. . .
