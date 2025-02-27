# GridapGmsh <img src="https://github.com/gridap/Gridap.jl/blob/master/images/color-logo-only.png" width="40" title="Gridap logo">

[![Build Status](https://github.com/gridap/GridapGmsh.jl/workflows/CI/badge.svg?branch=master)](https://github.com/gridap/GridapGmsh.jl/actions?query=workflow%3ACI)
[![Codecov](https://codecov.io/gh/gridap/GridapGmsh.jl/branch/master/graph/badge.svg)](https://codecov.io/gh/gridap/GridapGmsh.jl)


## Demo

Solve a Poisson problem with Gridap on top of a Finite Element mesh generated by GMSH. The mesh includes two physical groups, `"boundary1"` and `"boundary2"`, which are used to define boundary conditions. This is just a simple demo. Once the GMSH mesh is read, all the magic of Gridap can be applied to it. 

![](demo/demo-gmsh.png)

```julia
using Gridap
using GridapGmsh

model = GmshDiscreteModel("demo/demo.msh")

order = 1
diritags = ["boundary1", "boundary2"]

reffe = ReferenceFE(lagrangian,Float64,order)

V = TestFESpace(model,reffe,dirichlet_tags=["boundary1","boundary2"])

U = TrialFESpace(V,[0,1])

degree = 2
Ω = Triangulation(model)
dΩ = Measure(Ω,degree)

a(u,v) = ∫( ∇(v)⋅∇(u) )dΩ
l(v) = 0

op = AffineFEOperator(a,l,U,V)

uh = solve(op)
writevtk(Ω,"demo",cellfields=["uh"=>uh])
```

![](demo/demo.png)


## Installation

`GridapGmsh` is a registered package in the official [Julia package registry](https://github.com/JuliaRegistries/General).  Thus, the installation is done using the [Julia's package manager](https://julialang.github.io/Pkg.jl/v1/). Open the Julia REPL, type `]` to enter package mode, and install as follows
```julia
pkg> add GridapGmsh
```
## Installation requirements

`GridapGmsh` requires a working instalation of the GMSH Software Development Kit (SDK) available at [gmsh.info](https://gmsh.info/). There are two possible ways of helping `GridapGmsh` to find the GMSH installation.

- [Recommended] Set an environment variable called `GMSHROOT` containing the path to the location of the root folder of a GMSH installation. Make sure that: `$GMSHROOT/bin/gmsh` is the path of the GMSH binary and `$GMSHROOT/lib/gmsh.jl` is the path of the GMSH Julia API.

- Add the folder containing the GMSH binary to the `PATH`. This option will be ignored if the environment variable `GMSHROOT` is present. 

## Gotchas

- Gmsh does not allow to include entities of different dimension in the same physical group. In order to overcome this limitation, all physical groups defined in Gmesh with the same name will be merged in the same physical tag independently of their dimension.

- Vertices are always assigned to the corresponding CAD entity. However, this is not true for higher dimensional objects (i.e., edges, faces, cells). The later objects are associated with the right CAD entity if and only if they are present in a physical group of the same dimension of the object. If the object does not belong to a physical group of the same dimension, but it belongs to the closure of a higher dimensional object appearing in a physical group, then the low dimensional object receives the CAD id of the high dimensional object. If several high dimensional objects fulfill this requirement, we choose one arbitrary of the lowest dimension possible. This ensures, that edges and faces are assigned to the right CAD entity if the are in the interior of the CAD entity. The same is not true if the object is on the boundary of the CAD entity. In this case, include the corresponding object in a physical group if the right CAD ids are required.

## How to cite Gridap

In order to give credit to the `Gridap` contributors, we simply ask you to cite the reference below in any publication in which you have made use of `Gridap` packages:

```
@article{Badia2020,
  doi = {10.21105/joss.02520},
  url = {https://doi.org/10.21105/joss.02520},
  year = {2020},
  publisher = {The Open Journal},
  volume = {5},
  number = {52},
  pages = {2520},
  author = {Santiago Badia and Francesc Verdugo},
  title = {Gridap: An extensible Finite Element toolbox in Julia},
  journal = {Journal of Open Source Software}
}
```

## Contact

Please, contact the project administrators, [Santiago Badia](mailto:santiago.badia@monash.edu) and [Francesc Verdugo](mailto:fverdugo@cimne.upc.edu), for further questions about licenses and terms of use.

