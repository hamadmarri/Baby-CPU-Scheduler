# Baby-CPU-Scheduler

This is a very basic and lightweight yet very performant CPU scheduler.
You can use it for learning purposes as a base ground CPU scheduler on
Linux. Notice that many features are disabled to make this scheduler as
simple as possible. Baby Scheduler is very lightweight and powerful for
normal usage. I am using it as my main scheduler and sometimes I
switch back to CacULE for testing. The throughput in Baby Scheduler is
higher due to the task loadbalancer that I made for Baby Scheduler. The
loadbalancing is done via only one CPU which is CPU0 in which CPU0 scan
all other CPUs and move one task in every tick. The balancing is only
depending on the number of tasks (i.e. no load, weight or other factors).
Baby scheduler is only 1036 LOC where 254 LOC of it are just dependent
functions that are copied from CFS without any changes to let the
scheduler compile and run. You can find all Baby code is reduced in
bs.c, bs.h, and numa_fair.h. Baby scheduler picks next task that has
least vruntime, however, all CFS load/weight for task priority are
replaced with a simpler mechanism. Tasks priorities are injected in
vruntime where NICE0 priority task has a vruntime = real_exec_time,
but NICE-20 task has a vruntime < real_exec_time in which NICE-20 task
will run 20 more milliseconds than NICE0 one in a race time of 40ms.
See the equation in kernel/sched/bs.c:convert_to_vruntime.


