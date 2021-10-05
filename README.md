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

## Patch and Compile
### Patch
First, you need to patch the kernel source with one of the flavors of baby scheduler. See an example of patching linux kernel [here](https://github.com/hamadmarri/cacule-cpu-scheduler#how-to-apply-the-patch). You can use the same method to patch with baby instead of cacule.

1. Download the linux kernel (https://www.kernel.org/) that is same version as the patch (i.e if patch file name is baby-5.14.patch, then download https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.14.9.tar.xz)
2. Unzip linux kernel
3. Download baby patch file and place it inside the just unzipped linux kernel folder
4. cd linux-(version)
5. patch -p1 < baby-5.14.patch


### Configure
Baby uses periodic HZ only, and you need to disable other scheduler features such as `CGROUP_SCHED` and stat/debuging features.

run `make menuconfig`
and choose `CONFIG_HZ_PERIODIC`

You should see this when you run `cat .config | grep -i hz`:

```
CONFIG_HZ_PERIODIC=y
# CONFIG_NO_HZ_IDLE is not set
# CONFIG_NO_HZ_FULL is not set
# CONFIG_NO_HZ is not set
```

Then disable the following:
* CONFIG_EXPERT
* CONFIG_DEBUG_KERNEL
* CONFIG_SCHED_DEBUG
* CONFIG_SCHEDSTATS
* NO_HZ
* SCHED_AUTOGROUP
* CGROUP_SCHED
* UCLAMP_TASK
* SCHED_CORE


Make sure that `CONFIG_BS_SCHED` is selected (it appears at the top when running make menuconfig).
Confirm by running `cat .config | grep -i bs_sched`
```
CONFIG_BS_SCHED=y
```

Now compile `make` \
Then install the modules `sudo make modules_install` \
Then install the kernel `sudo make install`\
Reboot and choose the new kernel

To confirm that Baby is currently running:
```
$ dmesg | grep "Baby CPU"
Baby CPU scheduler (dl) v5.14 by Hamad Al Marri.
```
