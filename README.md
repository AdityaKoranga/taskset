
# Taskset Guide

## Introduction
`taskset` is a command-line utility that allows you to set or get the CPU affinity of a process. CPU affinity is a scheduler property that "bonds" a process to a given set of CPUs on the system, improving performance by reducing context-switching and cache misses. This guide explains how to use `taskset` to isolate cores and assign specific applications to them.

## Usage

### Assigning a Process to a Specific CPU Core

To start a new application on a specific core, use:

```bash
taskset -c core_number command_to_run_your_application
```

For example, to run `your_application` on core 0:

```bash
taskset -c 0 your_application
```

### Assigning a Process to Multiple Cores

You can also assign a process to run on multiple cores by listing the core numbers:

```bash
taskset -c 0,1,2 your_application
```

### Isolating Physical Cores

In systems with hyper-threading, each physical core is represented by multiple logical cores. If you wish to isolate a physical core for your application, include both logical cores in your `taskset` command.

#### Example: Isolating a Physical Core with Hyper-Threading

If CPU 0 has logical cores 0 and 40, to isolate this physical core, use:

```bash
taskset -c 0,40 your_application
```

This ensures `your_application` runs exclusively on the two logical cores of the physical core 0, utilizing the full processing power available.

## Verifying the Affinity

To check the affinity of a running process, use:

```bash
taskset -p process_id
```

Replace `process_id` with the PID of your application. This command will show you the CPU cores the process is allowed to run on.

## Understanding and Adjusting Process Affinity

The `taskset` command can also be used to retrieve and set the CPU affinity for running processes. CPU affinity defines the set of CPU cores on which a process can run. Adjusting the CPU affinity allows for performance tuning and ensuring that specific processes have dedicated CPU resources.

### Example: Viewing Process Affinity

```bash
taskset -p 2216
```

This command checks the CPU affinity for the process with PID 2216. An output like `pid 2216's current affinity mask: 10000001` indicates the process can run on CPUs identified by the binary positions set to `1` in the mask, in this case, CPUs 0 and 7.

### Adjusting Affinity

To change a process's CPU affinity, use the following syntax:

```bash
taskset -p affinity_mask pid
```

- `affinity_mask` is a hexadecimal representation of the CPUs the process is allowed to run on.
- `pid` is the Process ID to which the affinity mask will be applied.

#### Example: Assigning a Process to Specific CPUs

To allow a process to run only on CPU 0:

```bash
taskset -p 0x1 2216
```

To allow a process to run on CPUs 0 and 1:

```bash
taskset -p 0x3 2216
```

These adjustments can help improve the performance of CPU-bound processes by reducing context switching and ensuring cache locality.
