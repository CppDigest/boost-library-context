# #7 Resolved issue were variable var in a lambda test conflicted with struct... [Closed]

> Username: ismirlian  
> Created at: 2014-07-16 21:46:37 UTC  
> Updated at: 2014-07-29 15:45:23 UTC  
> Closed at: 2014-07-29 15:28:58 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/7  

... var in AIX's var.h.  
  
In ../boost/lambda/detail/lambda_functor_base.hpp there is variable called var that conflicts with an AIX system variable. Defining var and later undefining it frees that variable name.

---

## Comment 1

> Username: pdimov  
> Created_at: 2014-07-18 20:01:58 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/7#issuecomment-49473575  

Can you please post the definition of `var` in `var.h`? I want to see what's causing the conflict.

---

## Comment 2

> Username: ismirlian  
> Created_at: 2014-07-21 16:51:35 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/7#issuecomment-49632660  

yes I can show you definition. Sorry I took so long, I just wanted to clear it with my boss.  
  
#ifndef _H_VAR  
#define _H_VAR  
  
#include <sys/types.h>  
  
// Header structure for var structure  
struct var_hdr  
{  
        __ulong32_t var_vers;   // version number of var structure  
        __ulong32_t var_gen;    // generation number of var structure   
        __ulong32_t var_size;   // size of the of the var structure  
                                              // returned by the sysconfig syscall  
};  
  
// Structure for base kernel parameters, on the 32-bit kernel  
// RW in comment indicates a Read-Write variable alterable by sysconfig  
// RO in comment indicates a Read-Only variable not alterable by sysconfig  
  
struct var {  
    struct var_hdr  var_hdr;      /\* cfgvar header information  */  
  
```  
int     v_bufhw;               /* RW: buffer pool high-water mark  */  
int         v_mbufhw;      /* RW: max. mbufs high water mark   */  
int     v_maxup;               /* RW: max. # of user processes     */  
int     v_iostrun;             /* RW: enable disk i/o history      */  
int     v_leastpriv;       /* RW: least privilege enablement   */  
int     v_autost;              /* RW: automatic boot after halt    */  
int     v_maxpout;     /* RW: # of file pageouts at which  */  
                           /*     waiting occurs            */  
int     v_minpout;     /* RW: # of file pageout at which   */  
                           /*     ready occurs          */  
int     v_memscrub;    /* RW: memory scrubbing enabled      */  
  
int     v_lock;        /* RO: # entries in record lock table*/  
__cptr32    ve_lock;               /* RO: ptr to end of recordlock table*/  
int     v_file;        /* RO: # entries in open file table  */  
__cptr32    ve_file;               /* RO: ptr to end of open file table */  
int     v_proc;        /* RO: max # of system processes     */  
__cptr32    ve_proc;               /* RO: process table high water mark */  
int     v_clist;               /* RO: # of cblocks in cblock array  */  
int     v_thread;              /* RO: max # of system threads        */  
__cptr32    ve_thread;     /* RO: thread table high water mark  */  
__cptr32    vb_proc;               /* RO: beginning of process table    */  
__cptr32    vb_thread;     /* RO: beginning of thread table     */  
int     v_ncpus;               /* RO: number of active CPUs      */  
int     v_ncpus_cfg;       /* RO: number of processor configured*/  
int     v_fullcore;    /* RW: full core enabled (true/false)*/  
char        v_initlvl[4];      /* RW: init level             */  
char        v_pre430core;      /* RW: Use pre-430 core (true/false) */  
int     v_xmgc;        /* RW: xmalloc garbage collect delay */  
int             v_cpuguard;    /* RW: CPU Guarding Mode (true/false)*/  
int     v_ncargs;              /* RW: length of args,env for exec() */  
int             v_pre520tune;      /* RW: Use pre-520 Tuning Mode (t/f) */  
long long  v_hardstack;    /* RW: Hard stack max for 64-bit app */  
int     v_xmdbg_segs;      /* RW: Number of XMDBG segments      */  
int     v_max_logname;    /* RW: Maximum login name length     */  
int     v_num_xmfrecs;     /* RW: Number of XM free records     */  
long long v_sed_config;          /* RW: SED config  */  
long long   v_acl_config;          /* RW: ACL config                    */  
long long v_enhanced_rbac;  /* RW: Enhanced RBAC Mode */  
int     v_cluster_aware;    /* RW: Cluster is enabled */  
int     v_file_max;        /* RO: Max entries in open file tbl  */  
int     v_lock_max;        /* RO: Max entries in record lock tbl*/  
int     v_chown_restrict;   /* RW: chown_restrict mode (t/f)  */  
```  
  
};

---

## Comment 3

> Username: pdimov  
> Created_at: 2014-07-28 16:14:50 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/7#issuecomment-50360286  

I see that this can be a problem, but can't accept this request, sorry. It will break code that includes a smart_ptr header and then includes the system `<var.h>`.

---

## Comment 4

> Username: edelsohn  
> Created_at: 2014-07-28 19:30:05 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/7#issuecomment-50386734  

Would you please suggest an alternative solution to the conflict between the identifiers in the AIX header and Boost?

---

## Comment 5

> Username: glenfe  
> Created_at: 2014-07-28 22:58:28 UTC  
> Updated_at: 2014-07-28 22:59:25 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/7#issuecomment-50413881  

Instead of including `<sched.h>` we could just declare: `int sched_yield(void);`

---

## Comment 6

> Username: pdimov  
> Created_at: 2014-07-29 08:27:53 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/7#issuecomment-50448697  

Yes, this is an option.  
  
```  
#ifndef _AIX  
# include <sched.h>  
#else  
  // AIX's sched.h defines ::var which sometimes conflicts with Lambda's var  
  extern "C" int sched_yield(void);  
#endif  
```  
  
or whatever the definition of `sched_yield` in AIX's `<sched.h>`.

---

## Comment 7

> Username: ismirlian  
> Created_at: 2014-07-29 15:28:58 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/7#issuecomment-50492545  

Ok so I double checked the solution you suggested and I will be creating a new pull request shortly. I'll close this one.

---
