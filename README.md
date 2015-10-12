# sge-gpu
This is a package which is designed to add GPU scheduling capability to GE2011.11p1(not applicable to other versions).

Recompile the source needed. Secondly, you need to set a comsumable, named "ngpus". And assign value of it to each node.

When you submit your GPU job, you need to run:

>qsub -l ngpus=1 ...

This also works for mpi jobs.

It supports multiple GPU scheduling on one node. For example, if node001 has 4 GPUs installed. JobA uses GPU0, JobB uses GPU2, and then JobC requestes 2 GPUs, and the patched SGE can dispatch GPU1 and GPU3 to JobC, and set the environment for the job on node001:

CUDA_VISIBLE_DEVICES=1,3

See: http://sourceforge.net/projects/ge-gpu/?source=directory
