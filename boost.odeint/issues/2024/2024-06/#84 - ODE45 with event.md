# #84 - ODE45 with event [Open]

> Username: csb2024  
> Created at: 2024-06-29 23:25:16 UTC  
> Updated at: 2024-06-29 23:25:16 UTC  
> Url: https://github.com/boostorg/odeint/issues/84  

odeint Is there a way to implement matlab's ode45 event mechanism such as the following code:  
tspan=[0 t_max]; %Time span of simulation in sec  
Opt = odeset('Events', @myevent);  
[t,x]=ode45(@EOM,tspan,x0,Opt); function [value, isterminal]; %Time span of simulation in sec  
  
function [value, isterminal, direction] = myEvent(t, y)  
%value = (y(11) < 0);  
value = y(11); isterminal = 1; % myEvent(t, y)  
isterminal = 1; % Stop the integration  
direction = -1; end  
end
