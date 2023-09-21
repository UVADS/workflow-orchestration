# Workflow Orchestration

A library of approaches to running workflows and pipelines in a variety of environments.

Many data science projects involve running data through a multi-series workflow. Let's call
the end-to-end process a "workflow" and each step within it a "job." Jobs should be discrete
tasks specific to a logical step of the entire workflow. Here are some examples of jobs:

- Portions of ETL
- Fetching data from a remote source
- Running a recurring, scheduled task
- Performing analysis or ML models against data
- Ingesting a file

Jobs can be standalone scripts, containerized processes with all dependencies bundled, they
can be HPC (SLURM) or Kubernetes jobs, or compiled executables. The best approach to an
effective workflow is that each step is independent of the others, can be debugged or scaled
independently from other jobs, and that the developer preserve "loose coupling" between them.
Which is to say that you do not introduce a tight logic from one step to another.

If Process A runs and then forces its output directly to Process B, it is possible that
Process B is still busy with another job, or that it has errors, or otherwise unavailable.

Loose coupling in the context of workflows is an approach that makes use of some external
logic or orchestrator to know the state of all jobs within all workflows, and to help trigger
or queue the flow of work throughout the pipeline. Loose coupling in the context above means that
when Process A completes, it checks in with the workflow orchestrator to indicate the job is
complete, and the orchestrator either (1) prompts Process B to initiate (a PUSH model); 
or (2) queues the job for Process B to pick up (a PULL model) when it is available.
