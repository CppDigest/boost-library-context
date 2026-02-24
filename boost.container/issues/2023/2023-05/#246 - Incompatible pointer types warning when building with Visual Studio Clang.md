# #246 - Incompatible pointer types warning when building with Visual Studio Clang [Closed]

> Username: stertingen  
> Created at: 2023-05-12 12:49:36 UTC  
> Updated at: 2025-12-28 14:40:54 UTC  
> Closed at: 2025-12-28 14:40:54 UTC  
> Url: https://github.com/boostorg/container/issues/246  

This warning occurs when building with Clang from Visual Studio (using CMake). This affects  
- Clang 12.0.0 (from Visual Studio 2019) and Clang 15.0.1 (from Visual Studio 2022)  
- when building for x86 or amd64 target  
- in GNU and MSVC CLI mode.  
  
Using clang from MinGW or MSVC without Clang does not emit these warnings.  
  
Build log: https://pastebin.com/zQes4tph

---

## Comment 1

> Username: stertingen  
> Created at: 2023-05-15 11:56:42 UTC  
> Updated at: 2023-05-15 13:50:12 UTC  
> Url: https://github.com/boostorg/container/issues/246#issuecomment-1547715756  

I boiled it down to the following lines:  
```c  
  
/* First, define CAS_LOCK and CLEAR_LOCK on ints */  
/* Note CAS_LOCK defined to return 0 on success */  
  
#if defined(__GNUC__)&& (__GNUC__ > 4 || (__GNUC__ == 4 && __GNUC_MINOR__ >= 1))  
/* snip */  
#elif (defined(__GNUC__) && (defined(__i386__) || defined(__x86_64__)))  
/* snip */  
#else /* Win32 MSC */  
#define CAS_LOCK(sl)     interlockedexchange(sl, (LONG)1)  
#define CLEAR_LOCK(sl)   interlockedexchange (sl, (LONG)0)  
  
#endif /* ... gcc spins locks ... */  
  
/* snip */  
  
#define MLOCK_T               int  
#define TRY_LOCK(sl)          !CAS_LOCK(sl)  
#define RELEASE_LOCK(sl)      CLEAR_LOCK(sl)  
#define ACQUIRE_LOCK(sl)      (CAS_LOCK(sl)? spin_acquire_lock(sl) : 0)  
#define INITIAL_LOCK(sl)      (*sl = 0)  
#define DESTROY_LOCK(sl)      (0)  
static MLOCK_T malloc_global_mutex = 0;  
```  
  
`interlockedexchange` is defined for `long volatile *` while MLOCK_T is defined as `int`.  
  
Since clang tries to be kind of compatible to MSVC here, the __GNUC__ checks earlier on fail, so it uses the Win32 implementation with interlockedexchange. MSVC did not seem to mind or the warning has been silenced.  
  
Clang from MinGW selects another implementation, which also avoids the warning.  
  
To resolve this issue, I see two approaches:  
  
1. define `MLOCK_T` to `volatile long` if using the `interlockedexchange`-based implementation  
2. cast `sl` to `long volatile*` when using it: `#define CAS_LOCK(sl) interlockedexchange((LONG volatile*)sl, (LONG)1)`  
  
See also: https://learn.microsoft.com/en-us/windows/win32/api/winnt/nf-winnt-interlockedexchange  
  
EDIT: See also GitHub Action https://github.com/boostorg/container/actions/runs/4951665385/jobs/8856936413#step:4:21

---

## Comment 2

> Username: igaztanaga  
> Created at: 2025-12-28 14:40:54 UTC  
> Url: https://github.com/boostorg/container/issues/246#issuecomment-3694795316  

This bug was fixed in commit:  
  
https://github.com/boostorg/container/commit/74dfab45fc24d42f979487660c07bd4e99ab4159
