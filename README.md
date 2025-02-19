# BLISBLAS.jl

BLISBLAS.jl is a Julia package that allows users to use the [BLIS](https://github.com/flame/blis) library for Julia's underlying BLAS. Note that BLIS only provides BLAS but not LAPACK (OpenBLAS will still be used for LAPACK functionality).

This package is based on [libblastrampoline](https://github.com/JuliaLinearAlgebra/libblastrampoline), which enables picking a BLAS (and/or LAPACK) library at runtime, and thus **requires Julia 1.7+**.

## Installation

```
] add BLISBLAS
```

## Usage

Simply `using BLISBLAS` is enough to switch to BLIS for BLAS operations.

```julia
julia> using LinearAlgebra

julia> BLAS.get_config()
LinearAlgebra.BLAS.LBTConfig
Libraries: 
└ [ILP64] libopenblas64_.so

julia> using BLISBLAS

julia> BLAS.get_config()
LinearAlgebra.BLAS.LBTConfig
Libraries: 
├ [ILP64] libopenblas64_.so
└ [ILP64] libblis.so
```

Note that the BLISBLAS package has to be loaded in every new Julia process. Upon quitting and restarting, Julia will start with the default OpenBLAS.

## "Benchmark"
The following simple example is run with `OPENBLAS_NUM_THREADS=64` and `BLIS_NUM_THREADS=64` on a 64-core AMD EPYC 7763 (Milan) CPU.

```julia
julia> using BenchmarkTools

julia> A = rand(1000,1000); B = rand(1000,1000);

julia> @btime $A * $B;
  3.927 ms (2 allocations: 7.63 MiB)

julia> using BLISBLAS

julia> @btime $A * $B;
  2.729 ms (2 allocations: 7.63 MiB)
```

## Caveats

Currently, the LAPACK functions (OpenBLAS) don't use BLIS for BLAS operations but still the BLAS implementations provided by OpenBLAS. There are plans to fix this in a general manner, see https://github.com/JuliaPackaging/Yggdrasil/issues/2657 and https://github.com/JuliaPackaging/Yggdrasil/pull/2658.

## Related packages

* A more manual approach to accessing BLIS is provided by [BLIS.jl](https://github.com/JuliaLinearAlgebra/BLIS.jl)
* [MKL.jl](https://github.com/JuliaLinearAlgebra/MKL.jl) for Intel's MKL
