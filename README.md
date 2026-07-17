# Superchip Systems Papers

Curated papers on system optimization for Superchips, including NVIDIA GH200, GB200, and more.

This list focuses on tightly coupled CPU-GPU systems and rack-scale superchip platforms where system software, memory placement, data movement, power management, cooling, and workload scheduling materially affect performance.

## Scope

- NVIDIA GH200 Grace Hopper, GH200 NVL2, GB200 Grace Blackwell, GB200 NVL72, GB300, and future NVIDIA superchip systems
- Closely related integrated CPU-GPU platforms such as AMD MI300A when the paper helps compare design tradeoffs
- Topics: unified/coherent memory, system-allocated memory, NUMA/CDMM placement, offloading, LLM serving/training, HPC kernels, communication, power, energy, thermal, and rack-scale design

## Contents

- [Why Superchips?](#why-superchips)
- [Direct GH200 / GB200 Papers](#direct-gh200--gb200-papers)
- [AI Systems on Superchips](#ai-systems-on-superchips)
- [Memory, Data Movement, and Performance Models](#memory-data-movement-and-performance-models)
- [Power, Energy, Cooling, and Rack-Scale Systems](#power-energy-cooling-and-rack-scale-systems)
- [Adjacent Integrated CPU-GPU Systems](#adjacent-integrated-cpu-gpu-systems)
- [Architecture and Technical References](#architecture-and-technical-references)

## Why Superchips?

NVIDIA's superchip direction is a response to a systems problem: modern AI and HPC workloads are often limited by memory capacity, memory placement, CPU-GPU synchronization, GPU-GPU communication, and power/cooling density rather than raw FLOPs alone. Superchips move more of the system boundary into a tightly coupled CPU-GPU module, then extend that idea to rack-scale NVLink domains.

### How Superchips Differ from Other Designs

| Design | Examples | CPU-GPU relationship | Memory and communication model | What it is good at | Main system challenge |
| --- | --- | --- | --- | --- | --- |
| CPU-only server | x86 or Arm CPU node | No accelerator path | Coherent CPU memory only | Control-heavy, memory-rich, serial, and latency-sensitive work | Lower throughput and energy efficiency for dense AI/HPC kernels |
| PCIe-attached GPU server | x86/Arm host + PCIe GPUs | GPU is a discrete accelerator behind PCIe | CPU DRAM and GPU HBM are mostly separate; software relies on explicit copies, Unified Memory, or HMM | Flexible deployment and broad compatibility | PCIe bandwidth/latency, page migration, and CPU-GPU synchronization overhead |
| HGX / SXM GPU server | DGX/HGX H100 or B200 | GPUs are tightly connected to each other; CPU remains less tightly coupled | High GPU-GPU NVLink bandwidth, but CPU memory is not a first-class high-bandwidth GPU memory tier | Dense GPU training and GPU-heavy HPC | CPU-side orchestration and host-memory access can still become bottlenecks |
| Superchip | GH200, GB200 | Grace CPU and GPU(s) are connected in one coherent module through NVLink-C2C | CPU and GPU can directly address each other's memory; NUMA/CDMM policy becomes a first-order tuning issue | Mixed CPU-GPU workflows, larger working sets, memory oversubscription, and fine-grained CPU-GPU cooperation | New placement, migration, isolation, and runtime scheduling problems |
| Rack-scale superchip system | GB200 NVL72 | Many superchips are connected as a high-bandwidth NVLink domain | Rack-scale GPU communication and unified memory capacity through NVLink Switch | Trillion-parameter model training/inference, large simulation, and dense multi-GPU services | Power, cooling, collectives, placement, and multi-tenant scheduling at rack scale |

### Why NVIDIA Is Adopting Superchip Designs

- Reduce CPU-GPU data movement overhead: GH200 connects Grace and Hopper with coherent NVLink-C2C, giving the CPU and GPU a much faster and more coherent path than a conventional PCIe-attached accelerator design.
- Increase effective memory capacity: Grace LPDDR memory can act as a high-bandwidth memory tier that the GPU can directly address, helping workloads that exceed HBM capacity.
- Improve programmability for heterogeneous workloads: hardware coherence lets CPU and GPU threads share system-allocated memory more naturally, reducing the amount of manual data-copy code.
- Keep accelerators fed: tighter CPU-GPU coupling helps workloads with serial control logic, preprocessing, graph traversal, recommendation, database, sparse, or irregular phases that do not run entirely inside dense GPU kernels.
- Scale communication within the rack: GB200 NVL72 extends the superchip idea into a 72-GPU NVLink domain, reducing the communication penalty for model-parallel AI and tightly coupled HPC.
- Improve energy and density: moving data through shorter, higher-bandwidth links and designing the rack around liquid cooling and dense NVLink cabling makes the platform more viable for high-power AI factories and supercomputers.

For this repo, the most interesting papers are the ones that expose the tradeoffs behind those bullets: when coherent memory helps, when it hurts, how memory should be placed, which workloads become CPU-bound, how to schedule CPU-GPU transfers, and how power/cooling constraints affect full-system performance.

## Direct GH200 / GB200 Papers

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
| 2025 | [SuperOffload: Unleashing the Power of Large-Scale LLM Training on Superchips](https://arxiv.org/abs/2509.21271) | ASPLOS 2026 | GH200 | Superchip-centric LLM training offload using Hopper GPU, Grace CPU, and NVLink-C2C. |
| 2026 | [SuperInfer: SLO-Aware Rotary Scheduling and Memory Management for LLM Inference on Superchips](https://arxiv.org/abs/2601.20309) | MLSys 2026 | GH200 | KV-cache scheduling and full-duplex CPU-GPU transfer for latency SLOs. |
| 2026 | [Cross-Layer Energy Analysis of Multimodal Training on Grace Hopper Superchips](https://arxiv.org/abs/2605.01938) | arXiv | GH200 | Energy/performance tradeoffs for multimodal training with offloading, sequence parallelism, and hardware-aware scheduling. |

## Memory, Data Movement, and Performance Models

| Year | Paper | Venue | Platform | Notes |
| --- | --- | --- | --- | --- |
| 2025 | [Roofline Analysis of Tightly-Coupled CPU-GPU Superchips: A Study on MI300A and GH200](https://escholarship.org/uc/item/3zr637h8) | SC Workshops 2025 / P3HPC | GH200, MI300A | Extends roofline analysis to CPU-GPU contention, memory allocator effects, and arithmetic intensity. [DOI](https://doi.org/10.1145/3731599.3767497). |
| 2025 | [ARMing GPUs: On the Memory Subsystem of Grace Hopper GH200](https://sc25.conference-program.com/presentation/?id=misc131&sess=sess222) | SC25 HMEM invited talk | Quad GH200 | Memory subsystem characterization talk connected to the GH200 data-movement study. |

## Power, Energy, Cooling, and Rack-Scale Systems

| Year | Paper / Resource | Venue | Platform | Notes |
| --- | --- | --- | --- | --- |
| 2025 | [Alps, a versatile research infrastructure](https://doi.org/10.1145/3757348.3757365) | CUG 2025 | GH200, MI300A, A100 | System paper for the Alps supercomputer, including large-scale GH200 deployment context. |
| 2026 | [Power-Capping Metric Evaluation for Improving Energy Efficiency in HPC Applications](https://impact.ornl.gov/en/publications/power-capping-metric-evaluation-forimproving-energy-efficiency-in/) | ISC Workshops 2025 revised papers | GH200 | Runtime power-capping metrics for energy efficiency, including LSMS on GH200. |
| 2026 | [Generative Design for Direct-to-Chip Liquid Cooling for Data Centers](https://arxiv.org/abs/2604.10941) | arXiv | GB200 | Cold-plate channel design for GB200 Grace Blackwell thermal optimization. |

## Adjacent Integrated CPU-GPU Systems

| Year | Paper | Venue | Platform | Notes |
| --- | --- | --- | --- | --- |
| 2026 | [Inter-APU Communication on AMD MI300A Systems via Infinity Fabric: A Deep Dive](https://doi.org/10.1145/3767110.3767130) | MEMSYS 2025 | MI300A | Useful comparison point for coherent multi-APU memory and communication design. |
| 2026 | [Design and Implementation of Multi-Rail-Aware Hierarchical MPI Reduce-Scatter and Allgather Operations](https://doi.org/10.23919/ISC.2026.11520482) | ISC 2026 | GH200, H100, MI300A | Hierarchical collectives evaluated on Delta-AI GH200 and other accelerators. |

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
- HPC application optimization on GH200, GB200, or MI300A-like tightly coupled systems
- Power capping, power steering, energy modeling, and thermal design for superchip racks
- Communication libraries, collectives, and multi-node scaling on GH200 or GB200 clusters
