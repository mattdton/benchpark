.. Copyright 2023 Lawrence Livermore National Security, LLC and other
   Benchpark Project Developers. See the top-level COPYRIGHT file for details.

   SPDX-License-Identifier: Apache-2.0

=============================
Adding a System Specification
=============================

``benchpark/configs`` contains a directory for each system specified in Benchpark.
If your system is unlike the available configurations,
you can add a new directory with a name which identifies the system.

The naming convention for the systems is as following::

  SITE-[SYSTEMNAME-][INTEGRATOR]-MICROARCHITECTURE[-GPU][-NETWORK]

where::

  SITE = nosite | DATACENTERNAME

  SYSTEMNAME = the name of the specific system

  INTEGRATOR = COMPANY[_PRODUCTNAME][...]

  MICROARCHITECTURE = CPU Microarchitecture

  GPU = GPU Product Name

  NETWORK = Network Product Name

Benchpark has definitions for the following (nosite) systems:

- nosite-AWS_PCluster_Hpc7a-zen4-EFA

- nosite-HPECray-zen3-MI250X-Slingshot (same hardware as Frontier, Lumi, Tioga)

- nosite-x86_64 (x86 CPU only platform)



Benchpark has definitions for the following site-specific systems:

- LLNL-Magma-Penguin-icelake-OmniPath

- LLNL-Sierra-IBM-power9-V100-Infiniband (Sierra, Lassen)

- LLNL-Tioga-HPECray-zen3-MI250X-Slingshot


The following files are required for each nosite system ``benchpark/configs/${SYSTEM}``:

1. ``system_definition.yaml`` describes the system hardware, including the integrator (and the name of the product node or cluster type), the processor, (optionally) the accelerator, and the network; the information included here is what you will typically see recorded about the system on Top500.org.  We intend to make the system definitions in Benchpark searchable, and will add a schema to enforce consistency; until then, please copy the file and fill out all of the fields without changing the keys.  Also listed is the specific system the config was developed and tested on, as well as the known systems with the same hardware so that the users of those systems can find this system specification.

.. code-block:: yaml

  system_definition:
    name: HPECray-zen3-MI250X-Slingshot # or site-specific name, e.g., Frontier at ORNL
    site:
    system: HPECray-zen3-MI250X-Slingshot
    integrator:
      vendor: HPECray
      name: EX235a
    processor:
      vendor: AMD
      name: EPYC-Zen3
      ISA: x86_64
      uArch: zen3
    accelerator:
      vendor: AMD
      name: MI250X
      ISA: GCN
      uArch: gfx90a
    interconnect:
      vendor: HPECray
      name: Slingshot11
    system-tested:
      site: LLNL
      name: tioga
      installation-year: 2022
      description: [top500](https://www.top500.org/system/180052)
    top500-system-instances:
      - Frontier (ORNL)
      - Lumi     (CSC)
      - Tioga    (LLNL)


2. ``software.yaml`` defines default compiler and package names your package
manager (Spack) should use to build the benchmarks on this system.
``software.yaml`` becomes the spack section in the `Ramble configuration
file
<https://googlecloudplatform.github.io/ramble/configuration_files.html#spack-config>`_.

.. code-block:: yaml

    software:
      packages:
        default-compiler:
          pkg_spec: 'spack_spec_for_package'
        default-mpi:
          pkg_spec: 'spack_spec_for_package'

3. ``variables.yaml`` defines system-specific launcher and job scheduler.

.. code-block:: yaml

    variables:
      timeout: '30'
      scheduler: "slurm"
      sys_cores_per_node: "128"
      sys_gpus_per_node: "4"
      sys_mem_per_node unset
      max_request: "1000"  # n_ranks/n_nodes cannot exceed this
      n_ranks: '1000001'  # placeholder value
      n_nodes: '1000001'  # placeholder value
      batch_submit: "placeholder"
      mpi_command: "placeholder"
      # batch_queue: "pbatch"
      # batch_bank: "guest"

If defining a specific system, one can be more specific with available software versions
and packages, as demonstrated in :doc:`add-a-site-specific-system-config`.
