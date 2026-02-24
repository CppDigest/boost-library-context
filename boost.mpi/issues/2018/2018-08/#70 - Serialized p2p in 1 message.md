# #70 - Serialized p2p in 1 message [Closed]

> Username: aminiussi  
> Created at: 2018-08-20 12:36:01 UTC  
> Updated at: 2018-12-09 22:19:27 UTC  
> Closed at: 2018-12-09 22:19:27 UTC  
> Url: https://github.com/boostorg/mpi/issues/70  

Right now, serialized data is processed in 2 messages when transmitted point to point.  
Using MPI_Probe could be faster and maybe would solve #63

---

## Comment 1

> Username: aminiussi  
> Created at: 2018-09-06 17:56:56 UTC  
> Url: https://github.com/boostorg/mpi/issues/70#issuecomment-419185910  

@matthiastroyer @Belcourt  @hirschsn this should fix most problems mentioned in #63 while reducing the number of messages for serialized data (no more specific messages for archive sizes). I did not add the test yet, shame on me.  
  
The code is in branch [feature/70p2p-opt-probe](https://github.com/boostorg/mpi/tree/feature/70p2p-opt-probe)  
  
The modification is based on `MPI_[I][M]Probe` which is not working properly on IntelMPI2018 and older (expected to be fixed in current beta, need to check) which cannot be ignored IMO, so the implementation falls back on legacy protocol (and issues) for that compiler. Note that this is controlled by `request::probe_messages` property  (could be selected at run-time if useful ?) if other MPI implementations have the same issue.  
  
There have been major modification in order to allow that switch. Basically, request handler are now based on letter/envelope idiom and template factory method so to allow dynamic selection of the adapted implementation. Of course, user code is not impacted. Both codes (with and without probes) are compiled. Blocking send and recv also adapt to the feature selection so that blocking and non blocking send/ecv can be mixed.  
  
I'm going to do some cleanup and documentation but you have feedback/opinions/comments that would be great.  
  
Thanks

---

## Comment 2

> Username: aminiussi  
> Created at: 2018-12-09 22:19:27 UTC  
> Url: https://github.com/boostorg/mpi/issues/70#issuecomment-445577242  

Merged on develop
