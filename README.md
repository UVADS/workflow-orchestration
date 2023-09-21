# Workflow Orchestration

A library of approaches to running workflows and pipelines in a variety of environments.

## Workflows & Jobs

Many data science projects involve running data through a multi-series workflow. Let's call
the end-to-end process a "workflow" and each step within it a "job." Jobs should be discrete
tasks specific to a logical step of the entire workflow. Here are some examples of jobs:

- Portions of ETL
- Fetching data from a remote source
- Running a recurring, scheduled task
- Performing analysis or ML models against data
- Ingesting a file
- . . .

Jobs can be standalone scripts, containerized processes with all dependencies bundled, they
can be HPC (SLURM) or Kubernetes jobs, or compiled executables. The best approach to an
effective workflow is that each step is independent of the others, can be debugged or scaled
independently from other jobs, and that the developer preserve "loose coupling" between them.
Which is to say that you do not introduce a tight logic from one step to another.

## Loose Coupling

If Process A runs and then forces its output directly to Process B, it is possible that
Process B is still busy with another job, or that it has errors, or otherwise unavailable.

![Tight vs. Loose Coupling](./images/loose-coupling.png)

Loose coupling in the context of workflows is an approach that makes use of some external
logic or orchestrator to know the state of all jobs within all workflows, and to help trigger
or queue the flow of work throughout the pipeline. Loose coupling in the context above means that
when Process A completes, it checks in with the workflow orchestrator to indicate the job is
complete, and the orchestrator either (1) prompts Process B to initiate (a PUSH model); 
or (2) queues the job for Process B to pick up (a PULL model) when it is available.

## Orchestration Models

Let's consider three models for delivering a multi-step workflow on a compute system.

1. **List- or loop-based workflows** - 

2. **Workflow managers** - Many solutions to workflow/pipeline management have been created,
of varying qualities and with specific verticals or audiences in mind. Generally they
help the developer define, author, manage, and maintain workflows of varying complexity,
with varying levels of logic (if-then conditionals, error handling, etc.)

    Two noteworthy solutions are:

    A. **Apache Airflow**, first authored by AirBnb and open-sourced in 2016, is written in Python and can handle a variety of tasks, logic, and complexity. [Read more](https://airflow.apache.org/)

    B. **Cromwell**, created by the Broad Institute, is a "scientific workflow engine designed for simplicity and scalability." Cromwell enables jobs to be run on a variety of platforms (on-premise, cloud, HPC, etc.) within the same workflow. [Read more](https://github.com/broadinstitute/cromwell)

    A well-curated list of many other pipeline management tools is [available here](https://github.com/pditommaso/awesome-pipeline)

3. **Event-driven microservices** - 

