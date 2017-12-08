# Quick Start

```julia
Pkg.add("StaticArrays")  # or Pkg.clone("https://github.com/JuliaArrays/StaticArrays.jl")
using StaticArrays

# Create an SVector using various forms, using constructors, functions or macros
v1 = SVector(1, 2, 3)
v1.data === (1, 2, 3) # SVector uses a tuple for internal storage
v2 = SVector{3,Float64}(1, 2, 3) # length 3, eltype Float64
v3 = @SVector [1, 2, 3]
v4 = @SVector [i^2 for i = 1:10] # arbitrary comprehensions (range is evaluated at global scope)
v5 = zeros(SVector{3}) # defaults to Float64
v6 = @SVector zeros(3)
v7 = SVector{3}([1, 2, 3]) # Array conversions must specify size

# Can get size() from instance or type
size(v1) == (3,)
size(typeof(v1)) == (3,)

# Similar constructor syntax for matrices
m1 = SMatrix{2,2}(1, 2, 3, 4) # flat, column-major storage, equal to m2:
m2 = @SMatrix [ 1  3 ;
                2  4 ]
m3 = eye(SMatrix{3,3})
m4 = @SMatrix randn(4,4)
m5 = SMatrix{2,2}([1 3 ; 2 4]) # Array conversions must specify size

# Higher-dimensional support
a = @SArray randn(2, 2, 2, 2, 2, 2)

# Short-form macro for static vectors or matrices
m6 = @sa [1 2; 3 4]
m7 = @sa[1 2; 3 4] * @sa[5, 6] # without space or parenthesis, requires Julia v0.7 or above

# Supports all the common operations of AbstractArray
v7 = v1 + v2
v8 = sin.(v3)
v3 == m3 * v3 # recall that m3 = eye(SMatrix{3,3})
# map, reduce, broadcast, map!, broadcast!, etc...

# Indexing can also be done using static arrays of integers
v1[1] === 1
v1[SVector(3,2,1)] === @SVector [3, 2, 1]
v1[:] === v1
typeof(v1[[1,2,3]]) <: Vector # Can't determine size from the type of [1,2,3]

# Is (partially) hooked into BLAS, LAPACK, etc:
rand(MMatrix{20,20}) * rand(MMatrix{20,20}) # large matrices can use BLAS
eig(m3) # eig(), etc uses specialized algorithms up to 3×3, or else LAPACK

# Static arrays stay statically sized, even when used by Base functions, etc:
typeof(eig(m3)) == Tuple{SVector{3,Float64}, SMatrix{3,3,Float64,9}}

# similar() returns a mutable container, while similar_type() returns a constructor:
typeof(similar(m3)) == MMatrix{3,3,Float64,9} # (final parameter is length = 9)
similar_type(m3) == SMatrix{3,3,Float64,9}

# The Size trait is a compile-time constant representing the size
Size(m3) === Size(3,3)

# A standard Array can be wrapped into a SizedArray
m4 = Size(3,3)(rand(3,3))
inv(m4) # Take advantage of specialized fast methods

# reshape() uses Size() or types to specify size:
reshape([1,2,3,4], Size(2,2)) == @SMatrix [ 1  3 ;
                                            2  4 ]
typeof(reshape([1,2,3,4], Size(2,2))) === SizedArray{(2, 2),Int64,2,1}

```