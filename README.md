# MSCCL-Tests-NCCL

These tests check both the performance and the correctness of [MSCCL-EXECUTOR-NCCL](https://github.com/Azure/msccl-executor-nccl) operations.

## Build

To build the tests, just type `make`.

If CUDA is not installed in /usr/local/cuda, you may specify CUDA\_HOME. Similarly, if MSCCL-Tests-NCCL is not installed in /usr, you may specify MSCCL-Tests-NCCL\_HOME.

```sh
$ make CUDA_HOME=/path/to/cuda NCCL_HOME=/path/to/msccl-executor-nccl
```

MSCCL-Tests-NCCL tests rely on MPI to work on multiple processes, hence multiple nodes. If you want to compile the tests with MPI support, you need to set MPI=1 and set MPI\_HOME to the path where MPI is installed.

```sh
$ make MPI=1 MPI_HOME=/path/to/mpi CUDA_HOME=/path/to/cuda NCCL_HOME=/path/to/msccl-executor-nccl
```

## Usage

MSCCL-Tests-NCCL tests can run on multiple processes, multiple threads, and multiple CUDA devices per thread. The number of process is managed by MPI and is therefore not passed to the tests as argument. The total number of ranks (=CUDA devices) will be equal to (number of processes)\*(number of threads)\*(number of GPUs per thread).

### Quick examples

Run on 8 GPUs (`-g 8`), scanning from 8 Bytes to 128MBytes :
```sh
$ ./build/all_reduce_perf -b 8 -e 128M -f 2 -g 8
```

Run with MPI on 10 processes (potentially on multiple nodes) with 4 GPUs each, for a total of 40 GPUs:
```sh
$ mpirun -np 10 ./build/all_reduce_perf -b 8 -e 128M -f 2 -g 4
```

### Performance

See the [Performance](doc/PERFORMANCE.md) page for explanation about numbers, and in particular the "busbw" column.

### Arguments

All tests support the same set of arguments :

* Number of GPUs
  * `-t,--nthreads <num threads>` number of threads per process. Default : 1.
  * `-g,--ngpus <GPUs per thread>` number of gpus per thread. Default : 1.
* Sizes to scan
  * `-b,--minbytes <min size in bytes>` minimum size to start with. Default : 32M.
  * `-e,--maxbytes <max size in bytes>` maximum size to end at. Default : 32M.
  * Increments can be either fixed or a multiplication factor. Only one of those should be used
    * `-i,--stepbytes <increment size>` fixed increment between sizes. Default : (max-min)/10.
    * `-f,--stepfactor <increment factor>` multiplication factor between sizes. Default : disabled.
* MSCCL-Tests-NCCL operations arguments
  * `-o,--op <sum/prod/min/max/avg/all>` Specify which reduction operation to perform. Only relevant for reduction operations like Allreduce, Reduce or ReduceScatter. Default : Sum.
  * `-d,--datatype <datatype/all>` Specify which datatype to use. Default : Float. Currently we have "int8", "uint8", "int32", "uint32", "int64", "uint64", "half", "float", "double","bfloat16", "fp8_e4m3", "fp8_e5m2"
  * `-r,--root <root/all>` Specify which root to use. Only for operations with a root like broadcast or reduce. Default : 0.
* Performance
  * `-n,--iters <iteration count>` number of iterations. Default : 20.
  * `-w,--warmup_iters <warmup iteration count>` number of warmup iterations (not timed). Default : 5.
  * `-m,--agg_iters <aggregation count>` number of operations to aggregate together in each iteration. Default : 1.
  * `-a,--average <0/1/2/3>` Report performance as an average across all ranks (MPI=1 only). <0=Rank0,1=Avg,2=Min,3=Max>. Default : 1.
* Test operation
  * `-p,--parallel_init <0/1>` use threads to initialize MSCCL-Tests-NCCL in parallel. Default : 0.
  * `-c,--check <0/1>` check correctness of results. This can be quite slow on large numbers of GPUs. Default : 1.
  * `-z,--blocking <0/1>` Make MSCCL-Tests-NCCL collective blocking, i.e. have CPUs wait and sync after each collective. Default : 0.
  * `-G,--cudagraph <num graph launches>` Capture iterations as a CUDA graph and then replay specified number of times. Default : 0.

## Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit [CLA](https://cla.opensource.microsoft.com).

When you submit a pull request, a CLA bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

## Trademarks

This project may contain trademarks or logos for projects, products, or services. Authorized use of Microsoft
trademarks or logos is subject to and must follow
[Microsoft's Trademark & Brand Guidelines](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks/usage/general).
Use of Microsoft trademarks or logos in modified versions of this project must not cause confusion or imply Microsoft sponsorship.
Any use of third-party trademarks or logos are subject to those third-party's policies.

## Copyright

NCCL tests are provided under the BSD license. All source code and accompanying documentation is copyright (c) 2016-2021, NVIDIA CORPORATION. All rights reserved.

All modifications are copyright (c) 2021-2023, Microsoft Corporation. All rights reserved.

