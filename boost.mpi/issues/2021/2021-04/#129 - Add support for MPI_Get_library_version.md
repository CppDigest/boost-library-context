# #129 - Add support for MPI_Get_library_version [Closed]

> Username: leha-bot  
> Created at: 2021-04-21 20:36:32 UTC  
> Updated at: 2022-06-02 12:43:54 UTC  
> Closed at: 2022-06-02 12:43:54 UTC  
> Url: https://github.com/boostorg/mpi/issues/129  

It looks trivial, I could try to make a PR if you don't mind.  
  
Thanks for your attention

---

## Comment 1

> Username: aminiussi  
> Created at: 2021-04-22 07:09:22 UTC  
> Url: https://github.com/boostorg/mpi/issues/129#issuecomment-824598081  

Hi,  
  
There is a:  
```  
  /** @brief MPI version.  
   *  
   * Returns a pair with the version and sub-version number.  
   */  
static std::pair<int, int> version();  
```  
  
in the `environment` class.   
  
Is it enough ?  
  
Cheers

---

## Comment 2

> Username: leha-bot  
> Created at: 2021-04-23 12:11:51 UTC  
> Url: https://github.com/boostorg/mpi/issues/129#issuecomment-825615161  

Thanks for reply, it is pretty good function, but it returns a library version.As for Mpi_get_library_version(),it returns a string with library name (e.g. "Mpich",  "openmpi") : http://manpages.org/mpi_get_library_version/3

---

## Comment 3

> Username: aminiussi  
> Created at: 2021-04-26 06:45:45 UTC  
> Updated at: 2021-04-26 06:47:18 UTC  
> Url: https://github.com/boostorg/mpi/issues/129#issuecomment-826553477  

Oh right,   
  
A `static std::string environment::library_version() `  would be the way to go I guess, as we can't use `version`.  
  
If you're ok to submit a pull request that's fine with me. Otherwise I can just add it.
