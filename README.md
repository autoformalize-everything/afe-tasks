# afe-tasks

The task tree and fleet configuration for the afe fleet, run by
[afe-fleet](https://github.com/autoformalize-everything/afe-fleet).

```
fleet.toml                       coordination settings (lock repo, lease timings, admin)
tasks/<project>/project.toml     the code repo, base branch, agent image, prompts for a project
tasks/<task-id>/TASK.md          one task: what to do and what "done" means
tasks/<task-id>/task.toml        optional: verify command, deps, kinds, priority
tasks/<task-id>/done.json        written by the admin when a task is accepted
tasks/<task-id>/cancelled.json   written by the admin when a task is cancelled (a task removed
                                 from the tree keeps its directory with only this file)
STATUS.md                        written by the admin; for humans
```

A task is a directory with a `TASK.md` and no task below it; its id is the path under
`tasks/`. The nearest `project.toml` above it applies. Editing a task's directory changes its
`spec`: a worker holding it is stopped, and a PR written against the old text goes back to work.

Workers read only this repository and the lock branches in
[fleet-coord](https://github.com/autoformalize-everything/fleet-coord). Code changes arrive as
PRs to the project's repository, e.g. [afe-oss](https://github.com/autoformalize-everything/afe-oss).

| Task | Project | What |
|---|---|---|
| [`bzip2/surface-area`](tasks/bzip2/surface-area/TASK.md) | bzip2 | Document everything outside code can touch in bzip2 |
| [`bzip2/{spec,oracle,proofs,fuzz,tests}/…`](tasks/bzip2/README.md) (29) | bzip2 | Lean formalization: spec, executable oracle, proofs, differential fuzzer |
