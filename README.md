# opencode-config

My personal OpenCode Config, supporting for development. 


# Basic Development Flow

My basic flow for coding using OpenCode agents stems from an excellent article [Vibe Coding Our Way to Disaster](https://www.arthropod.software/p/vibe-coding-our-way-to-disaster) by Jake Nations. I modified the ideas in it and accommodated them for my needs.
You may find this useful, or not. The Commands in this repo support this flow. It consists of several steps. As you go, the agent documents its findings in a `/tasks` directory and uses that knowledge to build up on the previous steps.

Please note that this flow is mostly useful for **coding**. You, as a developer, still need to do some **thinking** beforehand and during.

Each stage has to be confirmed by a human. And ideally, the output of each stage should be reviewed and thought through by a human.

### Define Task

First, we need to create business definition of the task. What is it we want to do? How does it influence the system? How do we verify that the task is done? What are the acceptance criteria?

You can start with a short or long description and then let the agent ask the questions it needs. It is useful to assign a task number to the task to identify it later.

The outcome of this task is a Task Definition Document.

```
/task-define 1234 Write a short description of the task
```

### Research Task

After the task is defined, we let the agent research the current codebase. It will gather information about source code relevant for the task and often come up with follow up questions.

Often the research needs to happen outside of the source code. *This is not covered in this workflow.*

This produces a Research Document summarizing the current technical situation.

```
/task-research 1234
```

### Plan Task

In the task planning phase, the agent will roughly plan the implementation and if necessary, separate it into multiple milestones.

```
/task-plan 1234
```

### Implement Task

Finally in the implementation phase, the agent goes after each milestone, prepares detailed technical implementation and after it is approved, proceeds implementing the proposal.

```
/task-implement 1234
```

### Review source code

Review task uses `dotnet-reviewer` agent to review Security, Correctness, Architecture, Performance and Code Quality of code that is to be merged into develop branch.

```
/task-review
```