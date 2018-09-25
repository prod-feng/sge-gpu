# sge-gpu

Update: some bug fixes. April, 2018.

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

It supports multiple GPU scheduling on nodes as well. For example, if node1 and node2 each has 4 GPUs installed. On node1, JobA uses GPU0, JobB uses GPU2; on node2, jobC uses GPU 1 and GPU 2. And then JobZ requestes 4 GPUs, the patched SGE can dispatch GPU1 and GPU3 on node1, GPU0 and GPU3 on node2 to JobZ, and set the environment for the job on node1 as:

CUDA_VISIBLE_DEVICES=1,3  (0,2 are alreadt used by jobA and jobB)

and environment for the same job on node2 as:

CUDA_VISIBLE_DEVICES=0,3  (1,2 are already used by jobC)

For non-GPU jobs, CUDA_VISIBLE_DEVICES is set to be empty.

With this patch, you do not need wrapper or load sensor anymore to schedule multiple GPU jobs.

Check the link for downloading the sourse code: http://sourceforge.net/projects/ge-gpu/?source=directory


