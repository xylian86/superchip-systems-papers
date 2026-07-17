# Superchip Systems Papers

Curated papers on system optimization for Superchips, including NVIDIA GH200, GB200, and more.

This list focuses on tightly coupled CPU-GPU systems and rack-scale superchip platforms where system software, memory placement, data movement, power management, cooling, and workload scheduling materially affect performance.

## Contents

- [Why Superchips?](#why-superchips)
- [GH200 / GB200 System Evaluation and Optimization](#gh200--gb200-system-evaluation-and-optimization)
- [AI Systems on Superchips](#ai-systems-on-superchips)
- [Memory, Data Movement, and Performance Models](#memory-data-movement-and-performance-models)
- [Power, Energy, Cooling, and Rack-Scale Systems](#power-energy-cooling-and-rack-scale-systems)
- [Architecture and Technical References](#architecture-and-technical-references)

## Why Superchips?

Superchips address a growing systems bottleneck in AI and HPC: performance is increasingly constrained by memory capacity, data movement, CPU-GPU coordination, and power density, not only by accelerator peak FLOPs. NVIDIA's GH200 and GB200 designs integrate Grace CPUs with Hopper or Blackwell GPUs through high-bandwidth, coherent NVLink-C2C, making CPU memory, GPU memory, and CPU-side orchestration part of a more tightly coupled execution environment.

This architectural shift creates new opportunities and new system-software challenges. Superchips can reduce CPU-GPU transfer overhead, increase effective memory capacity, support finer-grained heterogeneous execution, and enable denser rack-scale NVLink systems. At the same time, they make memory placement, NUMA/CDMM policy, page migration, scheduling, isolation, power management, and cooling first-order optimization problems.

This repository collects papers that study those tradeoffs and show how applications, runtimes, libraries, and system software should be redesigned for superchip-based platforms.

## GH200 / GB200 System Evaluation and Optimization

| Year | Paper | Venue | Platform | Notes |
| --- | --- | --- | --- | --- |
| 2024 | [Harnessing Integrated CPU-GPU System Memory for HPC: a first look into Grace Hopper](https://arxiv.org/abs/2407.07850) | ICPP 2024 | GH200 | System-allocated memory, managed memory, first-touch, page migration, and memory oversubscription. |
| 2024 | [Understanding Data Movement in Tightly Coupled Heterogeneous Systems: A Case Study with the Grace Hopper Superchip](https://arxiv.org/abs/2408.11556) | arXiv | Quad GH200 | Intra-node and inter-node memory operations, memory placement, and communication behavior on Alps. |
| 2024 | [Automatic BLAS Offloading on Unified Memory Architecture: A Study on NVIDIA Grace-Hopper](https://arxiv.org/abs/2404.13195) | PEARC 2024 | GH200 | Runtime BLAS interception and GPU offload using coherent CPU-GPU memory. |
| 2024 | [Preliminary Performance Evaluation of Grace-Hopper GH200](https://doi.org/10.1109/CLUSTERWorkshops61563.2024.00050) | CLUSTER Workshops 2024 | GH200 | Early performance evaluation against H100 plus Sapphire Rapids systems. |
| 2025 | [GPU-CPU Shared Memory Performance Analysis on NVIDIA GH200](https://doi.org/10.1109/CLUSTERWorkshops65972.2025.11164213) | CLUSTER Workshops 2025 | GH200 | Shared-memory behavior under CUDA Unified Memory and NVLink-C2C. |
| 2025 | [NVIDIA GH200 ni okeru System-Allocated Memory no seino hyoka](https://ipsj.ixsq.nii.ac.jp/records/2001770) | IPSJ HPC 2025 | GH200 | Japanese technical report on system-allocated memory performance on Miyabi-G. |
| 2025 | [GH200 ni okeru denryoku seino saitekika](https://ipsj.ixsq.nii.ac.jp/records/2001771) | IPSJ HPC 2025 | GH200 | Japanese technical report on power/performance optimization on Miyabi-G and GH200 test systems. |
| 2026 | [Accelerating High-Order Finite Element Simulations at Extreme Scale with FP64 Tensor Cores](https://arxiv.org/abs/2603.09038) | arXiv | GH200, GB200 | FP64 Tensor Core and kernel fusion optimizations for MFEM at exascale. |

## AI Systems on Superchips

| Year | Paper | Venue | Platform | Notes |
| --- | --- | --- | --- | --- |
| 2025 | [Characterizing and Optimizing LLM Inference Workloads on CPU-GPU Coupled Architectures](https://arxiv.org/abs/2504.11750) | ISPASS 2025 | GH200, A100, H100 | Fine-grained LLM inference tracing; analyzes CPU-bound and GPU-bound regions on closely coupled GH200. |
| 2026 | [SuperOffload: Unleashing the Power of Large-Scale LLM Training on Superchips](https://arxiv.org/abs/2509.21271) | ASPLOS 2026 | GH200 | Superchip-centric LLM training offload using Hopper GPU, Grace CPU, and NVLink-C2C. |
| 2026 | [SuperInfer: SLO-Aware Rotary Scheduling and Memory Management for LLM Inference on Superchips](https://arxiv.org/abs/2601.20309) | MLSys 2026 | GH200 | KV-cache scheduling and full-duplex CPU-GPU transfer for latency SLOs. |
| 2026 | [No Buffer, No Bottleneck: Efficient Zero-Copy KV Cache Offloading for Long-Context LLMs](https://www.usenix.org/conference/osdi26/presentation/luo) | OSDI 2026 | GH200, GB200 | DirectKV enables GPU kernels to directly access CPU-resident KV cache over NVLink-C2C. |
| 2026 | [C2CServe: Leveraging NVLink-C2C for Elastic Serverless LLM Serving on MIG](https://arxiv.org/abs/2605.19481) | arXiv | GH200, GB200 | Serverless LLM serving with CPU-resident weights streamed to MIG instances over NVLink-C2C. |
| 2026 | [An Engineering Journey Training Large Language Models at Scale on Alps: The Apertus Experience](https://arxiv.org/abs/2604.12973) | arXiv | GH200 | Operational lessons from training a 70B open model on the Alps GH200 supercomputer. |
| 2026 | [Cross-Layer Energy Analysis of Multimodal Training on Grace Hopper Superchips](https://arxiv.org/abs/2605.01938) | arXiv | GH200 | Energy/performance tradeoffs for multimodal training with offloading, sequence parallelism, and hardware-aware scheduling. |

## Memory, Data Movement, and Performance Models

| Year | Paper | Venue | Platform | Notes |
| --- | --- | --- | --- | --- |
| 2025 | [Towards Memory Disaggregation via NVLink C2C: Benchmarking CPU-Requested GPU Memory Access](https://doi.org/10.1145/3723851.3723853) | HCDS 2025 | GH200 | Benchmarks Grace CPU access to GPU memory over NVLink-C2C for memory expansion. |
| 2025 | [Roofline Analysis of Tightly-Coupled CPU-GPU Superchips: A Study on MI300A and GH200](https://escholarship.org/uc/item/3zr637h8) | SC Workshops 2025 / P3HPC | GH200, MI300A | Extends roofline analysis to CPU-GPU contention, memory allocator effects, and arithmetic intensity. [DOI](https://doi.org/10.1145/3731599.3767497). |
| 2025 | [ARMing GPUs: On the Memory Subsystem of Grace Hopper GH200](https://sc25.conference-program.com/presentation/?id=misc131&sess=sess222) | SC25 HMEM invited talk | Quad GH200 | Memory subsystem characterization talk connected to the GH200 data-movement study. |
| 2026 | [Consistency and Coherence of the NVIDIA Grace-Hopper Superchip](https://doi.org/10.1145/3814942.3816134) | ISMM 2026 | GH200 | Empirical CPU-GPU consistency and coherence characterization for shared-memory software. |
| 2026 | [Taming GPU Underutilization via Static Partitioning and Fine-grained CPU Offloading](https://arxiv.org/abs/2604.08451) | ISC 2026 | GH200 | MIG resource sharing and memory offloading over cache-coherent NVLink-C2C. |

## Power, Energy, Cooling, and Rack-Scale Systems

| Year | Paper / Resource | Venue | Platform | Notes |
| --- | --- | --- | --- | --- |
| 2025 | [Preliminary Study on Fine-Grained Power and Energy Measurements on Grace Hopper GH200 with Open-Source Performance Tools](https://doi.org/10.1145/3703001.3724383) | HPC Asia 2025 Workshops | GH200 | Fine-grained GH200 power/energy profiling with hwmon, PAPI, and Score-P. |
| 2025 | [Alps, a versatile research infrastructure](https://doi.org/10.1145/3757348.3757365) | CUG 2025 | GH200, MI300A, A100 | System paper for the Alps supercomputer, including large-scale GH200 deployment context. |
| 2026 | [Power-Capping Metric Evaluation for Improving Energy Efficiency in HPC Applications](https://impact.ornl.gov/en/publications/power-capping-metric-evaluation-forimproving-energy-efficiency-in/) | ISC Workshops 2025 revised papers | GH200 | Runtime power-capping metrics for energy efficiency, including LSMS on GH200. |
| 2026 | [Generative Design for Direct-to-Chip Liquid Cooling for Data Centers](https://arxiv.org/abs/2604.10941) | arXiv | GB200 | Cold-plate channel design for GB200 Grace Blackwell thermal optimization. |

## Architecture and Technical References

These are not all research papers, but they are useful for understanding the hardware/software assumptions behind the papers above.

| Year | Resource | Platform | Notes |
| --- | --- | --- | --- |
| 2022 | [NVIDIA Grace Hopper Superchip Architecture In-Depth](https://developer.nvidia.com/blog/nvidia-grace-hopper-superchip-architecture-in-depth/) | GH200 | Architecture overview of Grace CPU, Hopper GPU, NVLink-C2C, and coherent memory. |
| 2024 | [NVIDIA GB200 NVL72 Delivers Trillion-Parameter LLM Training and Real-Time Inference](https://developer.nvidia.com/blog/nvidia-gb200-nvl72-delivers-trillion-parameter-llm-training-and-real-time-inference/) | GB200 NVL72 | Rack-scale Grace Blackwell architecture and NVLink domain overview. |
| 2024 | [NVIDIA Contributes NVIDIA GB200 NVL72 Designs to Open Compute Project](https://developer.nvidia.com/blog/nvidia-contributes-nvidia-gb200-nvl72-designs-to-open-compute-project/) | GB200 NVL72 | OCP design contribution, rack mechanics, liquid cooling, NVLink cartridges, and tray form factors. |
| 2024 | [NVIDIA GH200 Grace Hopper Superchip](https://www.nvidia.com/en-us/data-center/grace-hopper-superchip/) | GH200 | Official product page and specifications. |
| 2025 | [Understanding Memory Management on Hardware-Coherent Platforms](https://developer.nvidia.com/blog/understanding-memory-management-on-hardware-coherent-platforms/) | GH200, GB200, GB300 | NUMA versus CDMM memory management on hardware-coherent NVIDIA platforms. |
| 2026 | [NVIDIA GB200 NVL72](https://www.nvidia.com/en-us/data-center/gb200-nvl72/) | GB200, GB300 | Official product page and rack-scale specifications. |

## Contribution Format

Please use this format for new entries:

```markdown
| Year | [Paper Title](paper-url) | Venue | Platform | One-sentence note. |
```

Good candidate topics include:

- GH200 / GB200 memory allocation, NUMA, CDMM, page migration, and system-allocated memory
- LLM inference/training systems that exploit NVLink-C2C or coherent CPU-GPU memory
- HPC application optimization on GH200, GB200, or future NVIDIA superchip systems
- Power capping, power steering, energy modeling, and thermal design for superchip racks
- Communication libraries, collectives, and multi-node scaling on GH200 or GB200 clusters
