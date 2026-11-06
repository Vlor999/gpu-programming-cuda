# GPU programming: CUDA assignments

Three CUDA/C++ assignments from a GPU Programming course (SS 2025). Each assignment keeps its own git history, merged here from the original repositories: `projetCuda`, `projetCuda2` and `projetCuda3`.

| Directory | Program | Topic |
|---|---|---|
| `assignment-1` | `sc` | Stream compaction |
| `assignment-2` | `prefixsum` | Prefix sum |
| `assignment-3` | `3dgs-render` | Renderer (3dgs-render) |

## Build

Each assignment is a separate CMake project (CMake 3.16 or newer) and needs the CUDA toolkit and an NVIDIA GPU. From an assignment directory:

```
mkdir build && cd build
cmake .. -DCC=<compute capability>
make
```

Replace `<compute capability>` with the value for your GPU, for example `61`, `75` or `86`. The original READMEs list gcc 8 to 10 and Visual Studio 2017 or 2019 as supported toolchains, and CUDA 10.x, 11.x or 12.x for assignment 1.

## Assignment 1: stream compaction

Run `./sc [runs] [num-elements] [seed] [runCPU] [order-preserving] [in-place]`.

- `runs`: number of runs.
- `num-elements`: number of elements in the input stream.
- `seed`: PRNG seed used to generate the elements.
- `runCPU`: run the CPU solution first and compare it with the GPU result.
- `order-preserving`: order-preserving compaction.
- `in-place`: in-place compaction.

The GPU code is in `src/GPUStreamCompactor.cu`. The data is split into blocks processed by the GPU, and the block size was chosen from several test runs (see `challenge.md`). `commade.sh` builds with compute capability 61 and runs `./sc 10 1000000 42 0 1 1`.

## Assignment 2: prefix sum

Run `./prefixsum <input_dir>`. The input directory holds `frameinfo.json` and three `.dat` files. Two datasets are included in `data/`: `lego_back_128` and `lego_front_256`. The GPU code is in `src/prefixsum.cu`.

`challenge.md` records the optimization attempts:

- First: low shared memory use, one warp of threads per pixel, multithreading over the whole image.
- Second (unsuccessful): larger block sizes, and an attempt to use the CUB library that did not work on the author's system.
- Third: prefix sum, weight calculation and final color computed in a single kernel launch, using warp-level techniques. Replacing operations with bit shifts gave no gain.

Reported timings on the author's machine were about 600 ms per iteration for `lego_front_256` before optimization.

## Assignment 3: 3dgs-render

Run `./3dgs-render <input_dir>`. The input directory holds `frameinfo.json` and five `.dat` files. Two datasets are included in `data/`: `lego_front` and `ship_front_left`. The GPU code is in `src/render.cu`. The assignment statement is in `Assignment 3 2025.pdf`.

## Notes

- The framework code (CMake setup, `main.cpp`, dataset loading) comes from the course staff. See the git history for authorship.
- `assignment-2/data` contains files up to about 33 MB.
