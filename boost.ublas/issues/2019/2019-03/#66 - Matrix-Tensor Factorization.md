# #66 - Matrix/Tensor Factorization [Closed]

> Username: thomasyang1207  
> Created at: 2019-03-30 03:21:09 UTC  
> Updated at: 2019-05-18 18:41:16 UTC  
> Closed at: 2019-05-18 18:41:16 UTC  
> Url: https://github.com/boostorg/ublas/issues/66  

Operations such as matrix multiplication are BLAS level 3 operations, while matrix addition is level 1. Suppose we have the following computation of 4 matrices:   
  
A * C + B * D;   
  
Suppose A = B, but the user of the library may or may not know this. Would it be a good idea to find a way to compute this instead:   
  
A (C+ D)  
  
I think this is a rather trivial topic, but I thought it might be cool for uBLAS to have this

---

## Comment 1

> Username: yimyom  
> Created at: 2019-04-01 09:21:41 UTC  
> Url: https://github.com/boostorg/ublas/issues/66#issuecomment-478503077  

there was a project on optimizing the template tree with interesting  
results. It was done by an excellent student in 2014.  
  
It would be nice to revive this project and try to extend it. Are you  
interested?  
The project is here: https://github.com/BoostGSoC14/boost.ublas  
  
On Sat, Mar 30, 2019 at 2:21 PM Thomas <notifications@github.com> wrote:  
  
> Operations such as matrix multiplication are BLAS level 3 operations,  
> while matrix addition is level 1. Suppose we have the following computation  
> of 4 matrices:  
>  
> A * C + B * D;  
>  
> Suppose A = B, but the user of the library may or may not know this. Would  
> it be a good idea to find a way to compute this instead:  
>  
> A (C+ D)  
>  
> I think this is a rather trivial topic, but I thought it might be cool for  
> uBLAS to have this  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/ublas/issues/66>, or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ACDlcPrXj2gQ2aEuXROpx5DJ2zZFXi06ks5vbtgmgaJpZM4cTgZ4>  
> .  
>

---

## Comment 2

> Username: bassoy  
> Created at: 2019-04-01 15:45:18 UTC  
> Url: https://github.com/boostorg/ublas/issues/66#issuecomment-478633768  

Hi all,  
  
we are now trying to do this with Boost/Yap which is very convenient to use  
and has already a builtin transform function.  
  
Best  
Cem  
  
  
  
  
  
  
David Bellot <notifications@github.com> schrieb am Mo., 1. Apr. 2019, 11:21:  
  
> there was a project on optimizing the template tree with interesting  
> results. It was done by an excellent student in 2014.  
>  
> It would be nice to revive this project and try to extend it. Are you  
> interested?  
> The project is here: https://github.com/BoostGSoC14/boost.ublas  
>  
> On Sat, Mar 30, 2019 at 2:21 PM Thomas <notifications@github.com> wrote:  
>  
> > Operations such as matrix multiplication are BLAS level 3 operations,  
> > while matrix addition is level 1. Suppose we have the following  
> computation  
> > of 4 matrices:  
> >  
> > A * C + B * D;  
> >  
> > Suppose A = B, but the user of the library may or may not know this.  
> Would  
> > it be a good idea to find a way to compute this instead:  
> >  
> > A (C+ D)  
> >  
> > I think this is a rather trivial topic, but I thought it might be cool  
> for  
> > uBLAS to have this  
> >  
> > —  
> > You are receiving this because you are subscribed to this thread.  
> > Reply to this email directly, view it on GitHub  
> > <https://github.com/boostorg/ublas/issues/66>, or mute the thread  
> > <  
> https://github.com/notifications/unsubscribe-auth/ACDlcPrXj2gQ2aEuXROpx5DJ2zZFXi06ks5vbtgmgaJpZM4cTgZ4  
> >  
> > .  
> >  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/ublas/issues/66#issuecomment-478503077>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ADzxpcl4Ct5bds1K5fbHlwigssMCnu-Iks5vcc-mgaJpZM4cTgZ4>  
> .  
>
