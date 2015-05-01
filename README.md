# sge-gpu
This is a package which is designed to add GPU scheduling capability to GE2011.11p1(not capatable to other versions).
To let it work, you need to download the patched source files, and recompile your GE from source.

Secondly, you need to set a comsumable, named "ngpus". And assign value of it to each node.

When you submit your GPU job, you need to run:

>qsub -l ngpus=1 ...

This also works for mpi jobs.
