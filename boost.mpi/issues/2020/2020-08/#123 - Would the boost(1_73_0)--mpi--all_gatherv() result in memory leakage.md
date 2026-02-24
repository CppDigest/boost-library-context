# #123 - Would the boost(1_73_0)::mpi::all_gatherv() result in memory leakage? [Closed]

> Username: BinPeng-usst  
> Created at: 2020-08-02 08:28:26 UTC  
> Updated at: 2020-08-08 15:29:09 UTC  
> Closed at: 2020-08-08 15:27:53 UTC  
> Url: https://github.com/boostorg/mpi/issues/123  

Hi,  
  
I got an issue in my C++ codes.  
I tried to gather information as follows,  
"......  
std::vector<CPdPoint *> * m_pPdPointList;  
 std::vector<CPdPoint *> * s_pPdPointList;  
.......  
 boost::mpi::all_gatherv < CPdPoint * >(CWorld, *s_pPdPointList, *m_pPdPointList, m_iSendNum);  
......."  
The gathering WORKS but the MEMORY used by the program kept INCREASING.  
  
I have tried to replace the pointers  and changed to   
"......  
std::vector< CPdPoint >  m_P;  
 std::vector< CPdPoint >  s_P;  
.......  
 boost::mpi::all_gatherv < CPdPoint > (CWorld, s_P, m_P, m_iSendNum);  
......."  
  
For the change, the gathering doesn't WORK and errors threw out as follows,  
"......  
[0] application aborted  
aborting MPI_COMM_WORLD (comm=0x44000000), error -1, comm rank 0  
[1-2] terminated  
......"  
  
Any help or discussion is greatly appreciated.  
  
Peng

---

## Comment 1

> Username: aminiussi  
> Created at: 2020-08-04 08:35:03 UTC  
> Updated at: 2020-08-04 08:36:09 UTC  
> Url: https://github.com/boostorg/mpi/issues/123#issuecomment-668462978  

Hi,  
  
First, I'm not sure what the first example is trying to do. Communicating pointer fro one address space to another cannot work.  
  
What is the definition of CPdPoint ? is it serializable ?  
  
Do you have a complete self contained test case I can try ?  
  
Thanks

---

## Comment 2

> Username: BinPeng-usst  
> Created at: 2020-08-05 08:50:53 UTC  
> Updated at: 2020-08-05 09:09:42 UTC  
> Url: https://github.com/boostorg/mpi/issues/123#issuecomment-669069226  

Aminiussi,  
  
Thank you very much for your attention.  
  
Sorry i can't make a self contained test because the part relates to too many other data structures.  
I would like to explain as follows,  
  
In the first example, the **CPdPoint** is a complicated class and I have made it serializable.  
There are many CPdPoint objects. The pointer vector **m_pPdPointList** contains all the pointers to them and each vector **s_pPdPointList** contains a part of the pointers.   
I am trying to gather all the **s_pPdPointLists** in to a **m_pPdPointList**.  
  
The example does WORK and i have verified its results. The ONLY ISSUE is that the memory it used keeps increasing.   
  
I doubt there could be something wrong with the pointers, too.  
Therefore, i replaced all the pointers, i. e., using **std::vector< CPdPoint >** to replace **  std::vector< CPdPoint *>  ** without changing any other parts of the program.    
  
After the change, the gathering statement  
"boost::mpi::all_gatherv < CPdPoint > (CWorld, s_P, &m_P[0], m_iSendNum, 0);"  
is exactly the same as the BOOST funtion template,  
"template < typename T>   
  void gatherv(const communicator & comm, const std::vector< T > & in_values,   
               T * out_values, const std::vector< int > & sizes, int root);"  
  
However, the program fails.  
  
Hope i have made it clear :-)  
  
Thanks again.  
  
Peng

---

## Comment 3

> Username: aminiussi  
> Created at: 2020-08-06 19:23:08 UTC  
> Url: https://github.com/boostorg/mpi/issues/123#issuecomment-670146800  

Well, if that works:  
```  
"......  
std::vector<CPdPoint *> * m_pPdPointList;  
std::vector<CPdPoint *> * s_pPdPointList;  
.......  
boost::mpi::all_gatherv < CPdPoint * >(CWorld, *s_pPdPointList, *m_pPdPointList, m_iSendNum);  
......."  
The gathering WORKS but the MEMORY used by the program kept INCREASING.  
```  
it's probably for wrong reasons, the address of a point in one process can hardly have a meaning in another one.  
  
As for the later, there is not much that can be said without a self contained example. I've never encountered  such a problem before and Boost.MPI memory management is mostly automatic.  
  
You can try a valgrind run to track down that issue.  
  
Cheers

---

## Comment 4

> Username: BinPeng-usst  
> Created at: 2020-08-08 15:26:27 UTC  
> Updated at: 2020-08-08 15:27:16 UTC  
> Url: https://github.com/boostorg/mpi/issues/123#issuecomment-670941422  

Alain ，  
  
Thank you so much.  
You are right.  The issue roots in the improper using of pointers.  
I just replace all the pointers and it works properly now.  
  
Best regards  
  
Peng

---

## Comment 5

> Username: BinPeng-usst  
> Created at: 2020-08-08 15:29:09 UTC  
> Url: https://github.com/boostorg/mpi/issues/123#issuecomment-670941694  

Alain ，  
  
   
  
Thank you so much.  
  
You are right.  The issue roots in the improper using of pointers.  
  
I just replace all the pointers and it works properly now.  
  
   
  
Best regards  
  
   
  
Peng  
  
   
  
发件人: noreply@github.com <noreply@github.com> 代表 Alain Miniussi  
发送时间: 2020年8月7日 3:23  
收件人: boostorg/mpi <mpi@noreply.github.com>  
抄送: Bin Peng <BinPeng@usst.edu.cn>; Author <author@noreply.github.com>  
主题: Re: [boostorg/mpi] Would the boost(1_73_0)::mpi::all_gatherv() result in memory leakage? (#123)  
  
   
  
Well, if that works:  
  
"......  
std::vector<CPdPoint *> * m_pPdPointList;  
std::vector<CPdPoint *> * s_pPdPointList;  
.......  
boost::mpi::all_gatherv < CPdPoint * >(CWorld, *s_pPdPointList, *m_pPdPointList, m_iSendNum);  
......."  
The gathering WORKS but the MEMORY used by the program kept INCREASING.  
  
it's probably for wrong reasons, the address of a point in one process can hardly have a meaning in another one.  
  
As for the later, there is not much that can be said without a self contained example. I've never encountered such a problem before and Boost.MPI memory management is mostly automatic.  
  
You can try a valgrind run to track down that issue.  
  
Cheers  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub <https://github.com/boostorg/mpi/issues/123#issuecomment-670146800> , or unsubscribe <https://github.com/notifications/unsubscribe-auth/AMTF26SOLDZKTTEJACTVYV3R7L7KXANCNFSM4PSN3UOA> .  <https://github.com/notifications/beacon/AMTF26QKL2YJK74WAXUT6MLR7L7KXA5CNFSM4PSN3UOKYY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOE7Y2B4A.gif>
