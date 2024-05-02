# sge-gpu
### (For patched Son of GridEngine, plese check here: https://github.com/prod-feng/songe)

### To set a hybrid CPU and GPU cluster
Simultaneously run CPU jobs and GPUs jobs on the same node, one would better to reserve 1 CPU core per 1 GPU.
The way I do is:(suppose nodeA has 40CPU core, 2 GPUs) (there should be other ways)

1. Define a "rcpus" comsumable:

```
qconf -sc|grep rcpu
rcpus               rcpus      INT         <=    YES         YES        10       0
```
Here default debit is "10".

2. Set the value of the "rcpus" as:
```
40 cores - 2 cores(for GPU) =38 X 10 =380

rcpus=380+2 =382

```
3. Update the node
```
qconf -se nodeA
...
complex_values        slots=40,rcpus=382,ngpus=2
...
```
4. Name CPU queues as "cpu*", and GPU queues as "gpu*'
5. Add JSV to SGE conf
```
qconf -sconf

...
jsv_url                      /somewhere/mygpujsv.pl
...
```
In this "mygpujsv.pl" file, add the codes:
```

        if ( $key=~/gpu/) {
          jsv_sub_add_param('l_hard', 'rcpus',1);
        }
.......
(or add the following to make sure)

        if ( $key=~/cpu/) {
          jsv_sub_add_param('l_hard', 'rcpus',10);
        }
```
In this way, CPU jobs will debit 10 "rcpus", which can only use 38 cores; while, GPU jobs will debit 1 "rcpus". So reservs 2 CPU cores for GPU jobs.

====================================

Update 04/10/2024

Improve support fractioned -l ngpus=0.5. Useful for multithreading GPU jobs which needs multiple CPU cores, but 1 or several GPUs.

```
-pe openmp 10    # requests 10 cpu cores

-l ngpus=0.2     # so here 10X0.2=2 GPUs for this job on the same node.
```

Added protection for MT for multiple worker threads. Or set #SGE_ROOT/default/common/bootstrap to be:

```
listener_threads       1
worker_threads         1
```


And then restart the sgemaster service. The already running GPU jobs need to be re-submitted(the GPU info in SGE is not spooled).


NO Guarantee!

======================================

Update: Oct., 2018 

Add patched_files_ge2011.11.p1.0.1.tar.gz .

Fix a bug to support GPU array jobs properly. Only for GE2011.p1 now.

NO Guarantee!

======================================

Update: some bug fixes. April, 2018.

======================================

This is a package which is designed to enable GPU scheduling capability to GE2011.11p1(not applicable to other versions yet). With this patch, SGE can support GPU without any external Wraper tools.

Patch to Son of Grid Engine 8.18 is available too in the following link.

First, recompile and rebuild the source code. Second, you need to set a consumable, named "ngpus"(hard coded in the patched files). And assign value of it to each node. Like the following:

>$ qconf -sc

>#name               shortcut   type        relop requestable consumable default  urgency 

> ...

> ngpus               gpu        INT         <=    YES         YES        0        5000

>


and,

> qconf -se node1

> ...

> complex_values        slots=12,ngpus=2,...


When you submit a GPU job, you need to run the command:

>qsub -l ngpus=1 ...

This also works for parallel jobs.

>qsub -pe openmpi 4 -l ngpus=1 ...


Here, "-l ngpus=1" request 1 GPU for 1 process.

It supports multiple GPU scheduling on multiple nodes for parallel jobs(MPI, etc.) as well. For example, if node1 and node2 each has 4 GPUs installed. On node1, JobA uses GPU0, JobB uses GPU2; on node2, jobC uses GPU 1 and GPU 2. And then JobZ requestes 4 GPUs, the patched SGE can dispatch GPU1 and GPU3 on node1, GPU0 and GPU3 on node2 to JobZ, and set the environment for the job on node1 as:

CUDA_VISIBLE_DEVICES=1,3  (0,2 are alreadt used by jobA and jobB)

and environment for the same job on node2 as:

CUDA_VISIBLE_DEVICES=0,3  (1,2 are already used by jobC)

For non-GPU jobs, CUDA_VISIBLE_DEVICES is set to be empty.

With this patch, you do not need wrapper or load sensor anymore to schedule multiple GPU jobs.

Check the link for downloading the sourse code: http://sourceforge.net/projects/ge-gpu/?source=directory


