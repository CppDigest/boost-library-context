# #63 - Initializer list constructor for vector [Closed]

> Username: NAThompson  
> Created at: 2018-12-05 20:59:11 UTC  
> Updated at: 2023-07-16 23:48:46 UTC  
> Closed at: 2020-04-12 13:04:22 UTC  
> Url: https://github.com/boostorg/ublas/issues/63  

Initializer list constructors are not supported for ublas vector (error message from g++ 8):  
  
```bash  
 error: no matching function for call to 'boost::numeric::ublas::vector<double>::vector(<brace-enclosed initializer list>)'  
     boost::numeric::ublas::vector<double> w{1,2,3,4,5,6,7};  
```  
  
It would be a convenient enhancement, and presumably easy to implement.

---

## Comment 1

> Username: bassoy  
> Created at: 2019-03-04 06:49:09 UTC  
> Updated at: 2019-03-04 09:59:14 UTC  
> Url: https://github.com/boostorg/ublas/issues/63#issuecomment-469138096  

@NAThompson , thanks for your interest in uBLAS. This will be supported soon! We are making a transition from 2D to ND and also major changes to comply with C++11 and newer standards, see [example](https://github.com/boostorg/ublas/blob/develop/examples/tensor/construction_access.cpp).

---

## Comment 2

> Username: NAThompson  
> Created at: 2019-03-04 21:10:06 UTC  
> Url: https://github.com/boostorg/ublas/issues/63#issuecomment-469422745  

Awesome! Looks like y'all are doing really cool stuff.

---

## Comment 3

> Username: bassoy  
> Created at: 2020-04-12 13:04:22 UTC  
> Url: https://github.com/boostorg/ublas/issues/63#issuecomment-612611097  

Thanks.

---

## Comment 4

> Username: bradbell  
> Created at: 2023-07-16 23:48:46 UTC  
> Url: https://github.com/boostorg/ublas/issues/63#issuecomment-1637219767  

@bassoy This does not seem to work on Fedora 38 (which uses version 1.78.0 of boost).  See  
https://bugzilla.redhat.com/show_bug.cgi?id=2223035  
  
Would you please try the shell script below (boost_vector.sh) to see if the corresponding test works  for you:  
```  
#! /usr/bin/env bash  
set -u -e  
if [ $# != 1 ]  
then  
   echo 'usage: ./boost_vector.sh (std|boost)'  
   exit 1  
fi  
if [ "$1" == 'std' ]  
then  
   vector_type="std::vector<double>"  
else  
   vector_type="boost::numeric::ublas::vector<double>"  
fi  
cat << EOF > boost_vector.cpp  
# include <vector>  
# include <boost/numeric/ublas/vector.hpp>  
  
int main(void)  
{  $vector_type v = { 1.0, 2.0 };  
   if( v.size() == 2 && v[0] == 1.0 && v[1] == 2.0 )  
      return 0;  
   return 1;  
}  
EOF  
g++ boost_vector.cpp -o boost_vector  
if ! ./boost_vector  
then  
   echo 'boost_vector: Error'  
   exit 1  
fi  
#  
echo 'boost_vector: OK'  
exit 0  
```
