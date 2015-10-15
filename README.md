# sge-gpu
This is a package which is designed to enable GPU scheduling capability to GE2011.11p1(not applicable to other versions yet). With this patch, SGE can support GPU without any external Wraper tools.

First, recompile and rebuild the source code. Second, you need to set a consumable, named "ngpus"(hard coded in the patched files). And assign value of it to each node.

When you submit a GPU job, you need to run the command:

>qsub -l ngpus=1 ...

This also works for parallel jobs.

>qsub -pe openmpi 4 -l ngpus=1 ...


Here, "-l ngpus=1" request 1 GPU for 1 process.

It supports multiple GPU scheduling on one node as well. For example, if node001 has 4 GPUs installed. JobA uses GPU0, JobB uses GPU2, and then JobC requestes 2 GPUs, the patched SGE can dispatch GPU1 and GPU3 to JobC, and set the environment for the job on node001 as:

CUDA_VISIBLE_DEVICES=1,3

For non-GPU jobs, CUDA_VISIBLE_DEVICES is set to be empty.

With this patch, you do not need wrapper or load sensor anymore to schedule multiple GPU jobs.

Check the link for downloading the sourse code: http://sourceforge.net/projects/ge-gpu/?source=directory
