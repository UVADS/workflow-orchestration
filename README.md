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

Let's consider three general models for delivering a multi-step workflow on a compute system.

1. **List- or loop-based workflows** - Developers are likely already quite familiar with driving
a repetitive job off of a list of some type. This might be a local text file with a list of IDs,
an API offering lists or items to be processed, or a database table.

    The workflow fetches an ID to work with, and moves the file through each job in the
    workflow, step by step. Once the file is completed, the workflow triggers the next
    file to be processed.

    An advantage of this model is simplicity and maintenance. It allows one primary script (or SLURM job, etc.)
    to trigger multiple jobs, and for them all to share state within a single environment. (`env` variables,
    for instance, can be written to / read from the instance hosting the workflow.

    A disadvantage of this model is slow throughput, and the inability to run parallel work at the same time.

2. **Workflow managers** - Many solutions to workflow/pipeline management have been created,
of varying qualities and with specific verticals or audiences in mind. Generally they
help the developer define, author, manage, and maintain workflows of varying complexity,
with varying levels of logic (if-then conditionals, error handling, etc.)

    Three noteworthy solutions are:

      A. **Apache Airflow**, first authored by AirBnb and open-sourced in 2016, is written in Python and can handle a variety of tasks, logic, and complexity. [Read more](https://airflow.apache.org/)

    ![Apache Airflow](https://airflow.apache.org/docs/apache-airflow/stable/_images/arch-diag-basic.png)

      B. **Cromwell**, created by the Broad Institute, is a "scientific workflow engine designed for simplicity and scalability." Cromwell enables jobs to be run on a variety of platforms (on-premise, cloud, HPC, etc.) within the same workflow. [Read more](https://github.com/broadinstitute/cromwell)

    ![Cromwell](https://cromwell.readthedocs.io/en/stable/developers/bitesize/workflowExecution/WorkflowExecutionHighLevelOverview.png)

      C. **maestrowf**, sponsored by the Lawrence Livermore NL, is Python-based, installable as a `pip` package, and configured using readable YAML files. [Read more](https://github.com/LLNL/maestrowf)

    ![MaestroWF](./images/maestro.png)

    A well-curated list of many other pipeline management tools is [available here](https://github.com/pditommaso/awesome-pipeline)

3. **Event-driven microservices** - This solution tends to be more "cloud native," in that it is built with managed services in public cloud providers such as AWS, GCP, and Azure. Event-driven computing in AWS centers on either Lambda functions (triggered by one another using decoupled SQS queues, etc.) or Step Functions, which is a logical framework for executing Lambda functions and other software for more elaborate scenarios.

![AWS Step Functions](https://d1.awsstatic.com/step-functions-use-cases/use-case-diagram_AWS-Step-Functions_Video-on-demand-with-Elemental-Media-convert%402x.a4e26525bf649877d087c3b21a3f0ec3acedced9.png)
