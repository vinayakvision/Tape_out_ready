# Energy-Efficient Near-Memory Systolic Accelerator with On-Chip Weight Compression for Edge AI

> **ChipFoundry "Next Great Smart Application" Contest — Track: Edge IoT**
> Vinayak Venkappa Pujeri & Santosh Mokashi — VVCE, Mysuru (VTU Karnataka)
> License: Apache 2.0

---

## Table of Contents

- [Overview](#overview)
- [Documentation & Resources](#documentation--resources)
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Starting Your Project](#starting-your-project)
- [Development Flow](#development-flow)
- [GPIO Configuration](#gpio-configuration)
- [Local Precheck](#local-precheck)
- [Checklist for Shuttle Submission](#checklist-for-shuttle-submission)

---

## Overview

This project implements an **Energy-Efficient Near-Memory Systolic Accelerator with On-Chip Weight Compression** — a digital hardware IP integrated into the Caravel SoC user project area, targeting ultra-low-power TinyML inference for battery-operated **industrial anomaly detection sensor nodes (Edge IoT)**.
**# Energy-Efficient Near-Memory Systolic Accelerator with On-Chip Weight Compression for Edge AI

> **ChipFoundry "Next Great Smart Application" Contest — Track: Edge IoT**
> Vinayak Venkappa Pujeri & Santosh Mokashi — VVCE, Mysuru (VTU Karnataka)
> License: Apache 2.0

---

## Table of Contents

- [Overview](#overview)
- [Documentation & Resources](#documentation--resources)
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Starting Your Project](#starting-your-project)
- [Development Flow](#development-flow)
- [GPIO Configuration](#gpio-configuration)
- [Local Precheck](#local-precheck)
- [Checklist for Shuttle Submission](#checklist-for-shuttle-submission)

---

## Overview

This project implements an **Energy-Efficient Near-Memory Systolic Accelerator with On-Chip Weight Compression** — a digital hardware IP integrated into the Caravel SoC user project area, targeting ultra-low-power TinyML inference for battery-operated **industrial anomaly detection sensor nodes (Edge IoT)**.
### Energy-Efficient Near-Memory Systolic Accelerator with On-Chip Weight Compression for Edge AI

> **ChipFoundry "Next Great Smart Application" Contest — Track: Edge IoT**
> Vinayak Venkappa Pujeri & Santosh Mokashi — VVCE, Mysuru (VTU Karnataka)
> License: Apache 2.0

---

## Table of Contents

- [Overview](#overview)
- [Documentation & Resources](#documentation--resources)
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Starting Your Project](#starting-your-project)
- [Development Flow](#development-flow)
- [GPIO Configuration](#gpio-configuration)
- [Local Precheck](#local-precheck)
- [Checklist for Shuttle Submission](#checklist-for-shuttle-submission)

---

## Overview

This project implements an **Energy-Efficient Near-Memory Systolic Accelerator with On-Chip Weight Compression** — a digital hardware IP integrated into the Caravel SoC user project area, targeting ultra-low-power TinyML inference for battery-operated **industrial anomaly detection sensor nodes (Edge IoT)**.

**The core problem:** Edge AI inference devices spend 60–70% of their energy moving data, not computing. This accelerator attacks the memory bottleneck directly through three co-designed innovations:

1. **Near-Memory Compute** — Local OpenRAM SRAM banks placed adjacent to the systolic Processing Elements (PEs), reducing data movement energy and interconnect switching activity.
2. **Weight-Stationary Dataflow** — Weights remain stationary inside PE registers; INT8 activations flow left→right; partial sums accumulate top→bottom. Maximises PE utilisation for GEMM/Conv layers (MobileNet, TinyML).
3. **On-Chip INT4 Weight Compression** — Two 4-bit weights packed into one byte (~2× SRAM footprint reduction). A pipelined decompressor streams weights into PEs with zero throughput penalty.

**Caravel integration:** The accelerator wraps inside `user_project_wrapper.v` as a wishbone slave. Caravel's PicoRV32 RISC-V management core runs a C firmware driver that loads activation tiles, triggers computation, and reads back output partial sums via the wishbone bus.

**Key specifications:**

| Parameter | Value |
|-----------|-------|
| Array size | 4×4 weight-stationary systolic array |
| Activation precision | INT8 |
| Weight precision | INT4 (compressed) → INT8 (decompressed into PEs) |
| Weight SRAM reduction | ~2× vs uncompressed baseline |
| Target workloads | GEMM / Conv layers — MobileNet, TinyML |
| Process node | SKY130B |
| User area fit | Well within 10 mm² Caravel user project area |
| Top-level | `user_project_wrapper` (Caravel standard) |

**Architecture:**

```
┌──────────────────────────────────────────────────────┐
│               Top-Level FSM Controller                │
│         (Load → Decompress → Compute → Writeback)     │
│               Wishbone Slave Interface                 │
└─────────────────────┬────────────────────────────────┘
                      │
         ┌────────────┴────────────┐
         │                         │
┌────────▼─────┐         ┌─────────▼──────────┐
│  Activation  │         │  Weight Compression  │
│  Buffer      │         │  Engine (INT4)        │
│  (OpenRAM)   │         │  (OpenRAM)            │
└────────┬─────┘         └─────────┬────────────┘
         │                         │
┌────────▼─────┐         ┌─────────▼──────────┐
│  Activation  │         │  Weight Decompressor │
│  Router      │         │  (INT4 → INT8)       │
└────────┬─────┘         └─────────┬────────────┘
         │                         │
         └───────────┬─────────────┘
                     │
         ┌───────────▼─────────────┐
         │   4×4 Systolic PE Array  │
         │   (Weight-Stationary)    │
         │   PE → PE → PE → PE      │
         │   ↓    ↓    ↓    ↓       │
         │   PE → PE → PE → PE      │
         │   ↓    ↓    ↓    ↓       │
         │   PE → PE → PE → PE      │
         │   ↓    ↓    ↓    ↓       │
         │   PE → PE → PE → PE      │
         └───────────┬─────────────┘
                     │
         ┌───────────▼─────────────┐
         │       Output SRAM        │
         │  (Partial Sums → Host)   │
         └──────────────────────────┘
```

**Dataflow:** INT8 activations × INT4 weights (decompressed) → INT16/INT32 partial sums → Output SRAM → PicoRV32 readback via wishbone

**Team:**

| Name | Role | Contact |
|------|------|---------|
| Vinayak Venkappa Pujeri | RTL design, FSM, Caravel integration, OpenLane physical implementation, firmware, PCBA | vinayakpujeri2047@gmail.com |
| Santosh Mokashi | Verification (RTL + GLS), testbenches, Python golden reference, STA review, documentation | *(add email)* |

Both members: Final Year B.E. (ECE), Vidyavardhaka College of Engineering, Mysuru — VTU

> **Note on team size:** This is a 2-person team. The PCBA and mechanicals deliverables are scoped to a minimal carrier board schematic and simple enclosure. The silicon design, verification, and tapeout flow are fully prioritised.

---

## Documentation & Resources

| Resource | Link |
|----------|------|
| Caravel Datasheet | [caravel_datasheet.pdf](https://github.com/efabless/caravel/blob/master/doc/caravel_datasheet.pdf) |
| Caravel TRM (Register Maps) | [Caravel TRM](https://caravel-harness.readthedocs.io) |
| ChipFoundry Marketplace | [platform.chipfoundry.io](https://platform.chipfoundry.io) |
| OpenLane RTL-to-GDS | [github.com/The-OpenROAD-Project/OpenLane](https://github.com/The-OpenROAD-Project/OpenLane) |
| OpenRAM SKY130 SRAM macros | [openram.org](https://openram.org) |
| chipIgnite FAQs | [chipfoundry.io/faqs](https://chipfoundry.io/faqs) |
| ChipFoundry Discord | [Community Support](https://discord.gg/chipfoundry) |

---

## Prerequisites

Ensure your environment meets the following requirements:

- **Docker** — Linux / Windows / Mac (required for OpenLane and Caravel simulation)
- **Python 3.8+** with `pip`
- **Git** for repository management
- **ChipFoundry CLI** — installed via `pip install chipfoundry-cli`

---

## Project Structure

```
Directory                   Description
──────────────────────────────────────────────────────────────
openlane/                   OpenLane config for each macro and the wrapper
  ├── pe_array/             Hardening config for 4×4 systolic PE macro
  ├── compression_engine/   Hardening config for INT4 compression + decompressor
  └── user_project_wrapper/ Top-level wrapper hardening config
verilog/rtl/                RTL source (SystemVerilog / Verilog)
  ├── pe_cell.sv            Single Processing Element (MAC unit)
  ├── pe_array.sv           4×4 systolic array
  ├── compressor.sv         INT4 bit-packing engine
  ├── decompressor.sv       INT4 → INT8 pipelined decompressor
  ├── act_router.sv         Activation router (left→right feed)
  ├── top_fsm.sv            Top-level FSM (load/decompress/compute/writeback)
  ├── wb_slave.sv           Wishbone slave interface
  └── user_project_wrapper.v   Caravel top-level wrapper
verilog/gl/                 Gate-level netlists (generated after hardening)
verilog/dv/                 Verification testbenches (cocotb + Verilog)
  ├── pe_unit_tb/           PE cell unit testbench
  ├── system_tb/            Full 4×4 GEMM system testbench
  └── gls_tb/               Gate-level simulation testbench
gds/                        Final GDSII files (generated after hardening)
lef/                        LEF files for each hardened macro
firmware/                   C firmware for Caravel PicoRV32
  └── tile_loader.c         Wishbone tile-load and readback driver
pcba/                       KiCad carrier board schematic and layout
mechanicals/                FreeCAD sensor node enclosure (STEP file)
ai_logs/                    LLM prompt session logs (contest requirement)
scripts/                    Python utilities
  └── golden_ref.py         4×4 GEMM golden reference (INT8 × INT4 weights)
```

---

## Starting Your Project

### 1. Repository Setup

```bash
git clone <your-github-repo-URL>
pip install chipfoundry-cli
cd <project_name>
```

### 2. Project Initialization

> **Important:** Run this first before any other `cf` commands.

```bash
cf init
```

Creates `.cf/project.json` with project metadata. Required before `cf setup`, `cf gpio-config`, `cf harden`, `cf precheck`, and `cf verify`.

### 3. Environment Setup

```bash
cf setup
```

Installs: Caravel Lite, Management Core (PicoRV32), OpenLane, SKY130 PDK, and Timing Scripts.

---

## Development Flow

### Hardening the Design

#### Macro Hardening

Each sub-module is hardened individually before wrapper integration:

```bash
cf harden --list                   # List detected macro configurations
cf harden pe_array                 # Harden the 4×4 systolic PE macro
cf harden compression_engine       # Harden the INT4 compression + decompressor
```

#### Integration

Instantiate hardened macros in `verilog/rtl/user_project_wrapper.v`. Update `openlane/user_project_wrapper/config.json`:

```json
"VERILOG_FILES_BLACKBOX": "verilog/gl/pe_array.v verilog/gl/compression_engine.v",
"EXTRA_LEFS": "lef/pe_array.lef lef/compression_engine.lef",
"EXTRA_GDS_FILES": "gds/pe_array.gds gds/compression_engine.gds"
```

#### Wrapper Hardening

```bash
cf harden user_project_wrapper
```

### Verification

#### Step 1 — Configure GPIO (required before any verification)

```bash
cf gpio-config
```

#### Step 2 — RTL Simulation

```bash
cf verify pe_unit_test             # PE cell unit test (16 random vectors)
cf verify system_gemm_test         # Full 4×4 GEMM test vs Python golden reference
cf verify --all                    # Run all testbenches
```

#### Step 3 — Gate-Level Simulation

```bash
cf verify system_gemm_test --sim gl
```

#### Step 4 — Static Timing Analysis

```bash
make extract-parasitics
make create-spef-mapping
make caravel-sta
```

### Implementation Plan — Contest Timeline

**Final submission: April 30, 2025 | Shuttle tapeout: May 13, 2025**

| Week | Dates | Vinayak | Santosh |
|------|-------|---------|---------|
| **1** | Mar 26 – Apr 1 | Caravel repo + OpenLane setup, PE MAC RTL, INT4 compressor + decompressor RTL, OpenRAM macro selection | PE + compressor unit testbenches, Python golden reference (golden_ref.py) |
| **2** | Apr 2 – Apr 9 | 4×4 PE grid + activation router, top-level FSM, wishbone slave interface, OpenRAM macro integration | Full system testbench (10 GEMM test cases), STA `.sdc` first draft |
| **3** | Apr 10 – Apr 18 | OpenLane hardening (PE macro + wrapper), DRC/LVS fix iterations, clock-gating, ChipFoundry Precheck | GLS with back-annotated netlist, timing report review, Precheck error triage |
| **4** | Apr 19 – Apr 25 | C firmware (wishbone tile loader), KiCad carrier board schematic, FreeCAD enclosure | Firmware sim test, PPA benchmarking (compressed vs baseline), module documentation, demo video |
| **5** | Apr 26 – Apr 30 | Final GDS merge, Tapeout check, submission form | README finalisation, AI session logs committed to `ai_logs/` |

> **Critical path:** OpenLane hardening (Week 3) → Tapeout check (Week 5). DRC fix iterations must begin no later than April 10.

---

## GPIO Configuration

Configure GPIO power-on defaults before running verification or precheck:

```bash
cf gpio-config
```

Configures GPIO pins 5–37 interactively and auto-updates `verilog/rtl/user_defines.v`.

**Planned GPIO assignment for this project:**

| GPIO | Direction | Function |
|------|-----------|----------|
| GPIO[5:12] | Input | Activation data bus (8-bit) |
| GPIO[13:20] | Input | Compressed weight data bus (8-bit) |
| GPIO[21:28] | Output | Output partial sum bus (8-bit) |
| GPIO[29] | Input | Start signal |
| GPIO[30] | Output | Done signal |
| GPIO[31:37] | — | Reserved / unused |

> GPIO[0]–GPIO[4] are fixed system pins — do not modify.

---

## Local Precheck

> **GPIO configuration must be completed before running precheck.**

```bash
cf precheck                                          # Full precheck
cf precheck --disable-lvs                            # Skip LVS during early iterations
cf precheck --checks license --checks makefile       # Run specific checks only
```

All checks must pass (zero errors) before shuttle submission.

---

## Checklist for Shuttle Submission

- [ ] Top-level macro is named `user_project_wrapper`
- [ ] Full chip simulation passes for both RTL and GL
- [ ] Hardened macros are LVS and DRC clean
- [ ] `user_project_wrapper` matches the required pin order / template
- [ ] Design passes the local `cf precheck`
- [ ] C firmware committed to `firmware/` directory
- [ ] KiCad PCBA schematic committed to `pcba/` directory
- [ ] FreeCAD enclosure STEP file committed to `mechanicals/` directory
- [ ] AI / LLM session logs committed to `ai_logs/` directory (contest requirement)
- [ ] 3-minute demo video link added to this README
- [ ] Documentation (this README) updated with all project-specific details

**The core problem:** Edge AI inference devices spend 60–70% of their energy moving data, not computing. This accelerator attacks the memory bottleneck directly through three co-designed innovations:

1. **Near-Memory Compute** — Local OpenRAM SRAM banks placed adjacent to the systolic Processing Elements (PEs), reducing data movement energy and interconnect switching activity.
2. **Weight-Stationary Dataflow** — Weights remain stationary inside PE registers; INT8 activations flow left→right; partial sums accumulate top→bottom. Maximises PE utilisation for GEMM/Conv layers (MobileNet, TinyML).
3. **On-Chip INT4 Weight Compression** — Two 4-bit weights packed into one byte (~2× SRAM footprint reduction). A pipelined decompressor streams weights into PEs with zero throughput penalty.

**Caravel integration:** The accelerator wraps inside `user_project_wrapper.v` as a wishbone slave. Caravel's PicoRV32 RISC-V management core runs a C firmware driver that loads activation tiles, triggers computation, and reads back output partial sums via the wishbone bus.

**Key specifications:**

| Parameter | Value |
|-----------|-------|
| Array size | 4×4 weight-stationary systolic array |
| Activation precision | INT8 |
| Weight precision | INT4 (compressed) → INT8 (decompressed into PEs) |
| Weight SRAM reduction | ~2× vs uncompressed baseline |
| Target workloads | GEMM / Conv layers — MobileNet, TinyML |
| Process node | SKY130B |
| User area fit | Well within 10 mm² Caravel user project area |
| Top-level | `user_project_wrapper` (Caravel standard) |

**Architecture:**

```
┌──────────────────────────────────────────────────────┐
│               Top-Level FSM Controller                │
│         (Load → Decompress → Compute → Writeback)     │
│               Wishbone Slave Interface                 │
└─────────────────────┬────────────────────────────────┘
                      │
         ┌────────────┴────────────┐
         │                         │
┌────────▼─────┐         ┌─────────▼──────────┐
│  Activation  │         │  Weight Compression  │
│  Buffer      │         │  Engine (INT4)        │
│  (OpenRAM)   │         │  (OpenRAM)            │
└────────┬─────┘         └─────────┬────────────┘
         │                         │
┌────────▼─────┐         ┌─────────▼──────────┐
│  Activation  │         │  Weight Decompressor │
│  Router      │         │  (INT4 → INT8)       │
└────────┬─────┘         └─────────┬────────────┘
         │                         │
         └───────────┬─────────────┘
                     │
         ┌───────────▼─────────────┐
         │   4×4 Systolic PE Array  │
         │   (Weight-Stationary)    │
         │   PE → PE → PE → PE      │
         │   ↓    ↓    ↓    ↓       │
         │   PE → PE → PE → PE      │
         │   ↓    ↓    ↓    ↓       │
         │   PE → PE → PE → PE      │
         │   ↓    ↓    ↓    ↓       │
         │   PE → PE → PE → PE      │
         └───────────┬─────────────┘
                     │
         ┌───────────▼─────────────┐
         │       Output SRAM        │
         │  (Partial Sums → Host)   │
         └──────────────────────────┘
```

**Dataflow:** INT8 activations × INT4 weights (decompressed) → INT16/INT32 partial sums → Output SRAM → PicoRV32 readback via wishbone

**Team:**

| Name | Role | Contact |
|------|------|---------|
| Vinayak Venkappa Pujeri | RTL design, FSM, Caravel integration, OpenLane physical implementation, firmware, PCBA | vinayakpujeri2047@gmail.com |
| Santosh Mokashi | Verification (RTL + GLS), testbenches, Python golden reference, STA review, documentation | *(add email)* |

Both members: Final Year B.E. (ECE), Vidyavardhaka College of Engineering, Mysuru — VTU

> **Note on team size:** This is a 2-person team. The PCBA and mechanicals deliverables are scoped to a minimal carrier board schematic and simple enclosure. The silicon design, verification, and tapeout flow are fully prioritised.

---

## Documentation & Resources

| Resource | Link |
|----------|------|
| Caravel Datasheet | [caravel_datasheet.pdf](https://github.com/efabless/caravel/blob/master/doc/caravel_datasheet.pdf) |
| Caravel TRM (Register Maps) | [Caravel TRM](https://caravel-harness.readthedocs.io) |
| ChipFoundry Marketplace | [platform.chipfoundry.io](https://platform.chipfoundry.io) |
| OpenLane RTL-to-GDS | [github.com/The-OpenROAD-Project/OpenLane](https://github.com/The-OpenROAD-Project/OpenLane) |
| OpenRAM SKY130 SRAM macros | [openram.org](https://openram.org) |
| chipIgnite FAQs | [chipfoundry.io/faqs](https://chipfoundry.io/faqs) |
| ChipFoundry Discord | [Community Support](https://discord.gg/chipfoundry) |

---

## Prerequisites

Ensure your environment meets the following requirements:

- **Docker** — Linux / Windows / Mac (required for OpenLane and Caravel simulation)
- **Python 3.8+** with `pip`
- **Git** for repository management
- **ChipFoundry CLI** — installed via `pip install chipfoundry-cli`

---

## Project Structure

```
Directory                   Description
──────────────────────────────────────────────────────────────
openlane/                   OpenLane config for each macro and the wrapper
  ├── pe_array/             Hardening config for 4×4 systolic PE macro
  ├── compression_engine/   Hardening config for INT4 compression + decompressor
  └── user_project_wrapper/ Top-level wrapper hardening config
verilog/rtl/                RTL source (SystemVerilog / Verilog)
  ├── pe_cell.sv            Single Processing Element (MAC unit)
  ├── pe_array.sv           4×4 systolic array
  ├── compressor.sv         INT4 bit-packing engine
  ├── decompressor.sv       INT4 → INT8 pipelined decompressor
  ├── act_router.sv         Activation router (left→right feed)
  ├── top_fsm.sv            Top-level FSM (load/decompress/compute/writeback)
  ├── wb_slave.sv           Wishbone slave interface
  └── user_project_wrapper.v   Caravel top-level wrapper
verilog/gl/                 Gate-level netlists (generated after hardening)
verilog/dv/                 Verification testbenches (cocotb + Verilog)
  ├── pe_unit_tb/           PE cell unit testbench
  ├── system_tb/            Full 4×4 GEMM system testbench
  └── gls_tb/               Gate-level simulation testbench
gds/                        Final GDSII files (generated after hardening)
lef/                        LEF files for each hardened macro
firmware/                   C firmware for Caravel PicoRV32
  └── tile_loader.c         Wishbone tile-load and readback driver
pcba/                       KiCad carrier board schematic and layout
mechanicals/                FreeCAD sensor node enclosure (STEP file)
ai_logs/                    LLM prompt session logs (contest requirement)
scripts/                    Python utilities
  └── golden_ref.py         4×4 GEMM golden reference (INT8 × INT4 weights)
```

---

## Starting Your Project

### 1. Repository Setup

```bash
git clone <your-github-repo-URL>
pip install chipfoundry-cli
cd <project_name>
```

### 2. Project Initialization

> **Important:** Run this first before any other `cf` commands.

```bash
cf init
```

Creates `.cf/project.json` with project metadata. Required before `cf setup`, `cf gpio-config`, `cf harden`, `cf precheck`, and `cf verify`.

### 3. Environment Setup

```bash
cf setup
```

Installs: Caravel Lite, Management Core (PicoRV32), OpenLane, SKY130 PDK, and Timing Scripts.

---

## Development Flow

### Hardening the Design

#### Macro Hardening

Each sub-module is hardened individually before wrapper integration:

```bash
cf harden --list                   # List detected macro configurations
cf harden pe_array                 # Harden the 4×4 systolic PE macro
cf harden compression_engine       # Harden the INT4 compression + decompressor
```

#### Integration

Instantiate hardened macros in `verilog/rtl/user_project_wrapper.v`. Update `openlane/user_project_wrapper/config.json`:

```json
"VERILOG_FILES_BLACKBOX": "verilog/gl/pe_array.v verilog/gl/compression_engine.v",
"EXTRA_LEFS": "lef/pe_array.lef lef/compression_engine.lef",
"EXTRA_GDS_FILES": "gds/pe_array.gds gds/compression_engine.gds"
```

#### Wrapper Hardening

```bash
cf harden user_project_wrapper
```

### Verification

#### Step 1 — Configure GPIO (required before any verification)

```bash
cf gpio-config
```

#### Step 2 — RTL Simulation

```bash
cf verify pe_unit_test             # PE cell unit test (16 random vectors)
cf verify system_gemm_test         # Full 4×4 GEMM test vs Python golden reference
cf verify --all                    # Run all testbenches
```

#### Step 3 — Gate-Level Simulation

```bash
cf verify system_gemm_test --sim gl
```

#### Step 4 — Static Timing Analysis

```bash
make extract-parasitics
make create-spef-mapping
make caravel-sta
```

### Implementation Plan — Contest Timeline

**Final submission: April 30, 2025 | Shuttle tapeout: May 13, 2025**

| Week | Dates | Vinayak | Santosh |
|------|-------|---------|---------|
| **1** | Mar 26 – Apr 1 | Caravel repo + OpenLane setup, PE MAC RTL, INT4 compressor + decompressor RTL, OpenRAM macro selection | PE + compressor unit testbenches, Python golden reference (golden_ref.py) |
| **2** | Apr 2 – Apr 9 | 4×4 PE grid + activation router, top-level FSM, wishbone slave interface, OpenRAM macro integration | Full system testbench (10 GEMM test cases), STA `.sdc` first draft |
| **3** | Apr 10 – Apr 18 | OpenLane hardening (PE macro + wrapper), DRC/LVS fix iterations, clock-gating, ChipFoundry Precheck | GLS with back-annotated netlist, timing report review, Precheck error triage |
| **4** | Apr 19 – Apr 25 | C firmware (wishbone tile loader), KiCad carrier board schematic, FreeCAD enclosure | Firmware sim test, PPA benchmarking (compressed vs baseline), module documentation, demo video |
| **5** | Apr 26 – Apr 30 | Final GDS merge, Tapeout check, submission form | README finalisation, AI session logs committed to `ai_logs/` |

> **Critical path:** OpenLane hardening (Week 3) → Tapeout check (Week 5). DRC fix iterations must begin no later than April 10.

---

## GPIO Configuration

Configure GPIO power-on defaults before running verification or precheck:

```bash
cf gpio-config
```

Configures GPIO pins 5–37 interactively and auto-updates `verilog/rtl/user_defines.v`.

**Planned GPIO assignment for this project:**

| GPIO | Direction | Function |
|------|-----------|----------|
| GPIO[5:12] | Input | Activation data bus (8-bit) |
| GPIO[13:20] | Input | Compressed weight data bus (8-bit) |
| GPIO[21:28] | Output | Output partial sum bus (8-bit) |
| GPIO[29] | Input | Start signal |
| GPIO[30] | Output | Done signal |
| GPIO[31:37] | — | Reserved / unused |

> GPIO[0]–GPIO[4] are fixed system pins — do not modify.

---

## Local Precheck

> **GPIO configuration must be completed before running precheck.**

```bash
cf precheck                                          # Full precheck
cf precheck --disable-lvs                            # Skip LVS during early iterations
cf precheck --checks license --checks makefile       # Run specific checks only
```

All checks must pass (zero errors) before shuttle submission.

---

## Checklist for Shuttle Submission

- [ ] Top-level macro is named `user_project_wrapper`
- [ ] Full chip simulation passes for both RTL and GL
- [ ] Hardened macros are LVS and DRC clean
- [ ] `user_project_wrapper` matches the required pin order / template
- [ ] Design passes the local `cf precheck`
- [ ] C firmware committed to `firmware/` directory
- [ ] KiCad PCBA schematic committed to `pcba/` directory
- [ ] FreeCAD enclosure STEP file committed to `mechanicals/` directory
- [ ] AI / LLM session logs committed to `ai_logs/` directory (contest requirement)
- [ ] 3-minute demo video link added to this README
- [ ] Documentation (this README) updated with all project-specific details

**The core problem:** Edge AI inference devices spend 60–70% of their energy moving data, not computing. This accelerator attacks the memory bottleneck directly through three co-designed innovations:

1. **Near-Memory Compute** — Local OpenRAM SRAM banks placed adjacent to the systolic Processing Elements (PEs), reducing data movement energy and interconnect switching activity.
2. **Weight-Stationary Dataflow** — Weights remain stationary inside PE registers; INT8 activations flow left→right; partial sums accumulate top→bottom. Maximises PE utilisation for GEMM/Conv layers (MobileNet, TinyML).
3. **On-Chip INT4 Weight Compression** — Two 4-bit weights packed into one byte (~2× SRAM footprint reduction). A pipelined decompressor streams weights into PEs with zero throughput penalty.

**Caravel integration:** The accelerator wraps inside `user_project_wrapper.v` as a wishbone slave. Caravel's PicoRV32 RISC-V management core runs a C firmware driver that loads activation tiles, triggers computation, and reads back output partial sums via the wishbone bus.

**Key specifications:**

| Parameter | Value |
|-----------|-------|
| Array size | 4×4 weight-stationary systolic array |
| Activation precision | INT8 |
| Weight precision | INT4 (compressed) → INT8 (decompressed into PEs) |
| Weight SRAM reduction | ~2× vs uncompressed baseline |
| Target workloads | GEMM / Conv layers — MobileNet, TinyML |
| Process node | SKY130B |
| User area fit | Well within 10 mm² Caravel user project area |
| Top-level | `user_project_wrapper` (Caravel standard) |

**Architecture:**

```
┌──────────────────────────────────────────────────────┐
│               Top-Level FSM Controller                │
│         (Load → Decompress → Compute → Writeback)     │
│               Wishbone Slave Interface                 │
└─────────────────────┬────────────────────────────────┘
                      │
         ┌────────────┴────────────┐
         │                         │
┌────────▼─────┐         ┌─────────▼──────────┐
│  Activation  │         │  Weight Compression  │
│  Buffer      │         │  Engine (INT4)        │
│  (OpenRAM)   │         │  (OpenRAM)            │
└────────┬─────┘         └─────────┬────────────┘
         │                         │
┌────────▼─────┐         ┌─────────▼──────────┐
│  Activation  │         │  Weight Decompressor │
│  Router      │         │  (INT4 → INT8)       │
└────────┬─────┘         └─────────┬────────────┘
         │                         │
         └───────────┬─────────────┘
                     │
         ┌───────────▼─────────────┐
         │   4×4 Systolic PE Array  │
         │   (Weight-Stationary)    │
         │   PE → PE → PE → PE      │
         │   ↓    ↓    ↓    ↓       │
         │   PE → PE → PE → PE      │
         │   ↓    ↓    ↓    ↓       │
         │   PE → PE → PE → PE      │
         │   ↓    ↓    ↓    ↓       │
         │   PE → PE → PE → PE      │
         └───────────┬─────────────┘
                     │
         ┌───────────▼─────────────┐
         │       Output SRAM        │
         │  (Partial Sums → Host)   │
         └──────────────────────────┘
```

**Dataflow:** INT8 activations × INT4 weights (decompressed) → INT16/INT32 partial sums → Output SRAM → PicoRV32 readback via wishbone

**Team:**

| Name | Role | Contact |
|------|------|---------|
| Vinayak Venkappa Pujeri | RTL design, FSM, Caravel integration, OpenLane physical implementation, firmware, PCBA | vinayakpujeri2047@gmail.com |
| Santosh Mokashi | Verification (RTL + GLS), testbenches, Python golden reference, STA review, documentation | *(add email)* |

Both members: Final Year B.E. (ECE), Vidyavardhaka College of Engineering, Mysuru — VTU

> **Note on team size:** This is a 2-person team. The PCBA and mechanicals deliverables are scoped to a minimal carrier board schematic and simple enclosure. The silicon design, verification, and tapeout flow are fully prioritised.

---

## Documentation & Resources

| Resource | Link |
|----------|------|
| Caravel Datasheet | [caravel_datasheet.pdf](https://github.com/efabless/caravel/blob/master/doc/caravel_datasheet.pdf) |
| Caravel TRM (Register Maps) | [Caravel TRM](https://caravel-harness.readthedocs.io) |
| ChipFoundry Marketplace | [platform.chipfoundry.io](https://platform.chipfoundry.io) |
| OpenLane RTL-to-GDS | [github.com/The-OpenROAD-Project/OpenLane](https://github.com/The-OpenROAD-Project/OpenLane) |
| OpenRAM SKY130 SRAM macros | [openram.org](https://openram.org) |
| chipIgnite FAQs | [chipfoundry.io/faqs](https://chipfoundry.io/faqs) |
| ChipFoundry Discord | [Community Support](https://discord.gg/chipfoundry) |

---

## Prerequisites

Ensure your environment meets the following requirements:

- **Docker** — Linux / Windows / Mac (required for OpenLane and Caravel simulation)
- **Python 3.8+** with `pip`
- **Git** for repository management
- **ChipFoundry CLI** — installed via `pip install chipfoundry-cli`

---

## Project Structure

```
Directory                   Description
──────────────────────────────────────────────────────────────
openlane/                   OpenLane config for each macro and the wrapper
  ├── pe_array/             Hardening config for 4×4 systolic PE macro
  ├── compression_engine/   Hardening config for INT4 compression + decompressor
  └── user_project_wrapper/ Top-level wrapper hardening config
verilog/rtl/                RTL source (SystemVerilog / Verilog)
  ├── pe_cell.sv            Single Processing Element (MAC unit)
  ├── pe_array.sv           4×4 systolic array
  ├── compressor.sv         INT4 bit-packing engine
  ├── decompressor.sv       INT4 → INT8 pipelined decompressor
  ├── act_router.sv         Activation router (left→right feed)
  ├── top_fsm.sv            Top-level FSM (load/decompress/compute/writeback)
  ├── wb_slave.sv           Wishbone slave interface
  └── user_project_wrapper.v   Caravel top-level wrapper
verilog/gl/                 Gate-level netlists (generated after hardening)
verilog/dv/                 Verification testbenches (cocotb + Verilog)
  ├── pe_unit_tb/           PE cell unit testbench
  ├── system_tb/            Full 4×4 GEMM system testbench
  └── gls_tb/               Gate-level simulation testbench
gds/                        Final GDSII files (generated after hardening)
lef/                        LEF files for each hardened macro
firmware/                   C firmware for Caravel PicoRV32
  └── tile_loader.c         Wishbone tile-load and readback driver
pcba/                       KiCad carrier board schematic and layout
mechanicals/                FreeCAD sensor node enclosure (STEP file)
ai_logs/                    LLM prompt session logs (contest requirement)
scripts/                    Python utilities
  └── golden_ref.py         4×4 GEMM golden reference (INT8 × INT4 weights)
```

---

## Starting Your Project

### 1. Repository Setup

```bash
git clone <your-github-repo-URL>
pip install chipfoundry-cli
cd <project_name>
```

### 2. Project Initialization

> **Important:** Run this first before any other `cf` commands.

```bash
cf init
```

Creates `.cf/project.json` with project metadata. Required before `cf setup`, `cf gpio-config`, `cf harden`, `cf precheck`, and `cf verify`.

### 3. Environment Setup

```bash
cf setup
```

Installs: Caravel Lite, Management Core (PicoRV32), OpenLane, SKY130 PDK, and Timing Scripts.

---

## Development Flow

### Hardening the Design

#### Macro Hardening

Each sub-module is hardened individually before wrapper integration:

```bash
cf harden --list                   # List detected macro configurations
cf harden pe_array                 # Harden the 4×4 systolic PE macro
cf harden compression_engine       # Harden the INT4 compression + decompressor
```

#### Integration

Instantiate hardened macros in `verilog/rtl/user_project_wrapper.v`. Update `openlane/user_project_wrapper/config.json`:

```json
"VERILOG_FILES_BLACKBOX": "verilog/gl/pe_array.v verilog/gl/compression_engine.v",
"EXTRA_LEFS": "lef/pe_array.lef lef/compression_engine.lef",
"EXTRA_GDS_FILES": "gds/pe_array.gds gds/compression_engine.gds"
```

#### Wrapper Hardening

```bash
cf harden user_project_wrapper
```

### Verification

#### Step 1 — Configure GPIO (required before any verification)

```bash
cf gpio-config
```

#### Step 2 — RTL Simulation

```bash
cf verify pe_unit_test             # PE cell unit test (16 random vectors)
cf verify system_gemm_test         # Full 4×4 GEMM test vs Python golden reference
cf verify --all                    # Run all testbenches
```

#### Step 3 — Gate-Level Simulation

```bash
cf verify system_gemm_test --sim gl
```

#### Step 4 — Static Timing Analysis

```bash
make extract-parasitics
make create-spef-mapping
make caravel-sta
```

### Implementation Plan — Contest Timeline

**Final submission: April 30, 2025 | Shuttle tapeout: May 13, 2025**

| Week | Dates | Vinayak | Santosh |
|------|-------|---------|---------|
| **1** | Mar 26 – Apr 1 | Caravel repo + OpenLane setup, PE MAC RTL, INT4 compressor + decompressor RTL, OpenRAM macro selection | PE + compressor unit testbenches, Python golden reference (golden_ref.py) |
| **2** | Apr 2 – Apr 9 | 4×4 PE grid + activation router, top-level FSM, wishbone slave interface, OpenRAM macro integration | Full system testbench (10 GEMM test cases), STA `.sdc` first draft |
| **3** | Apr 10 – Apr 18 | OpenLane hardening (PE macro + wrapper), DRC/LVS fix iterations, clock-gating, ChipFoundry Precheck | GLS with back-annotated netlist, timing report review, Precheck error triage |
| **4** | Apr 19 – Apr 25 | C firmware (wishbone tile loader), KiCad carrier board schematic, FreeCAD enclosure | Firmware sim test, PPA benchmarking (compressed vs baseline), module documentation, demo video |
| **5** | Apr 26 – Apr 30 | Final GDS merge, Tapeout check, submission form | README finalisation, AI session logs committed to `ai_logs/` |

> **Critical path:** OpenLane hardening (Week 3) → Tapeout check (Week 5). DRC fix iterations must begin no later than April 10.

---

## GPIO Configuration

Configure GPIO power-on defaults before running verification or precheck:

```bash
cf gpio-config
```

Configures GPIO pins 5–37 interactively and auto-updates `verilog/rtl/user_defines.v`.

**Planned GPIO assignment for this project:**

| GPIO | Direction | Function |
|------|-----------|----------|
| GPIO[5:12] | Input | Activation data bus (8-bit) |
| GPIO[13:20] | Input | Compressed weight data bus (8-bit) |
| GPIO[21:28] | Output | Output partial sum bus (8-bit) |
| GPIO[29] | Input | Start signal |
| GPIO[30] | Output | Done signal |
| GPIO[31:37] | — | Reserved / unused |

> GPIO[0]–GPIO[4] are fixed system pins — do not modify.

---

## Local Precheck

> **GPIO configuration must be completed before running precheck.**

```bash
cf precheck                                          # Full precheck
cf precheck --disable-lvs                            # Skip LVS during early iterations
cf precheck --checks license --checks makefile       # Run specific checks only
```

All checks must pass (zero errors) before shuttle submission.

---

## Checklist for Shuttle Submission

- [ ] Top-level macro is named `user_project_wrapper`
- [ ] Full chip simulation passes for both RTL and GL
- [ ] Hardened macros are LVS and DRC clean
- [ ] `user_project_wrapper` matches the required pin order / template
- [ ] Design passes the local `cf precheck`
- [ ] C firmware committed to `firmware/` directory
- [ ] KiCad PCBA schematic committed to `pcba/` directory
- [ ] FreeCAD enclosure STEP file committed to `mechanicals/` directory
- [ ] AI / LLM session logs committed to `ai_logs/` directory (contest requirement)
- [ ] 3-minute demo video link added to this README
- [ ] Documentation (this README) updated with all project-specific details
