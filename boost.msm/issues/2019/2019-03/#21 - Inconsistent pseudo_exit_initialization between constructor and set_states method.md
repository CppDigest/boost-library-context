# #21 - Inconsistent pseudo_exit_initialization between constructor and set_states method [Closed]

> Username: davidfrulm  
> Created at: 2019-03-13 09:05:53 UTC  
> Updated at: 2024-02-17 14:43:46 UTC  
> Closed at: 2024-02-17 14:43:46 UTC  
> Url: https://github.com/boostorg/msm/issues/21  

I have following nested structure: SubSubFsm [with PseudoExitState] -> SubFsm [with PseudoExitState] -> Fsm  
  
If I set the SubFsms with the Fsm default constructor or using boost::msm::back::states_  in the Fsm constructor, pseudo exit states are correctly set.  
  
If I set the SubFsms using "set_states", a link is somehow missing between SubFsm and Fsm that makes that ExitEvent from PseudoExitState in the SubFsm to be handled in that PseudoExitState. This makes the "no-transition" function to be invoked [for no reason ?]. After that, ExitEvent is forwarded to parent Fsm, where will be finally handled.  
  
As such, this bug is more cosmetic than any other thing as the Fsm at the end works but with an extra handling of the "no_transition" function in the SubFsm.  
  
I attached a minimal code example that shows the problem. In addition, I copy here below the output of such code example for reference:  
  
SubFsms created in constructor  
Fsm - on_entry  
SubFsm - on_entry  
SubSubFsm - on_entry  
State - on_entry  
State - on_exit  
SubSubFsm - on_exit  
SubFsm - on_exit  
TerminateState - on_entry  
  
SubFsms created through set_states method  
Fsm - on_entry  
SubFsm - on_entry  
SubSubFsm - on_entry  
State - on_entry  
State - on_exit  
SubSubFsm - on_exit  
SubFsm: no transition from state 1 on event 9ExitEvent  
SubFsm - on_exit  
TerminateState - on_entry  
  
[main.zip](https://github.com/boostorg/msm/files/2960545/main.zip)

---

## Comment 1

> Username: henry-ch  
> Created at: 2024-02-13 10:59:19 UTC  
> Url: https://github.com/boostorg/msm/issues/21#issuecomment-1941211118  

I cannot reproduce the error reported here:  
  
- There is no SubSubFsm in the code  
- Output is the same in both cases.  
  
SubFsms created in constructor  
Fsm - on_entry  
SubFsm - on_entry  
State - on_entry  
State - on_exit  
SubFsm - on_exit  
TerminateState - on_entry  
  
SubFsms created through set_states method  
Fsm - on_entry  
SubFsm - on_entry  
State - on_entry  
State - on_exit  
SubFsm - on_exit  
TerminateState - on_entry  
  
Ok to close?
