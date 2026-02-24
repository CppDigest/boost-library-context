# #53 - Revisit Boost reviews and implement what is meaningful [Closed]

> Username: mzimbres  
> Created at: 2023-01-28 11:19:06 UTC  
> Updated at: 2023-05-30 21:11:10 UTC  
> Closed at: 2023-05-30 21:11:09 UTC  
> Url: https://github.com/boostorg/redis/issues/53  

In addition to the conditions for acceptance other suggestions must be reviewed and implemented when possible/meaningful. Reviews can be found at: https://lists.boost.org/Archives/boost/2023/01/date.php  
  
Links to individual reviews can be found below  
  
1. Zach Laine: https://lists.boost.org/Archives/boost/2023/01/253883.php  
2. Vinnie Falco: https://lists.boost.org/Archives/boost/2023/01/253886.php  
3. Christian Mazakas: https://lists.boost.org/Archives/boost/2023/01/253900.php  
4. Ruben Perez: https://lists.boost.org/Archives/boost/2023/01/253915.php  
5. Dmitry Arkhipov: https://lists.boost.org/Archives/boost/2023/01/253925.php  
6. Alan de Freitas: https://lists.boost.org/Archives/boost/2023/01/253927.php  
7. Mohammad Nejati: https://lists.boost.org/Archives/boost/2023/01/253929.php  
8. Sam Hartsfield: https://lists.boost.org/Archives/boost/2023/01/253931.php  
9. Miguel Portilla: https://lists.boost.org/Archives/boost/2023/01/253935.php  
10. Robert A.H. Leahy: https://lists.boost.org/Archives/boost/2023/01/253928.php  
  
The thread with the ACCEPT from the review manager can be found here: https://lists.boost.org/Archives/boost/2023/01/253944.php.

---

## Comment 1

> Username: maxwellb  
> Created at: 2023-01-31 17:40:31 UTC  
> Url: https://github.com/boostorg/redis/issues/53#issuecomment-1410802330  

Regarding the suggestion to offer enum values for command names in place of requiring strings, please continue to allow strings for command names. A configuration file may still rename commands, though it has been labeled "deprecated" since Redis 6.0.

---

## Comment 2

> Username: mzimbres  
> Created at: 2023-05-30 21:11:09 UTC  
> Url: https://github.com/boostorg/redis/issues/53#issuecomment-1569108964  

Most code review requirements have been implemented by now.
