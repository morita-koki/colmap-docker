# Colmap and Ceres Solver with Docker

- if we want to use colmap with gpu support, we need to build cedres-solver with gpu support.
- we can use docker to build colmap and ceres-solver with gpu support.

## Quick Start

- build docker image

```bash
./build.sh
```

- run docker container

```bash
./run.sh /path/to/your/workspace
```

- run colmap gui

```bash
./run-gui.sh
```

# LICENSE is following the original repository

- [colmap](https://github.com/colmap/colmap)
