# .vcon-repo

Machine-readable internal knowledge graph for this repository.

- **graph.json** — Hand-maintained. Nodes are this repo's documents plus the ecosystem repositories the spec kit describes; edges are `references`, `describes`, `implements`, `depends_on`, and `tracks_bugs_in`. Subsystems group repositories by role (core pipeline, ingress adapters, transcription, user-facing apps, spec-kit docs).

## For Robots

Load `graph.json` to navigate the spec kit and the ecosystem it documents. Each `document` node carries a `file` path relative to the repo root; each `repo` node names a sibling repository with its primary language and role. Edges trace how docs relate to each other, to the IETF spec, and to the repos they describe. The `subsystems` array groups nodes by feature.

To find the right doc for a task, start from [CLAUDE.md](../CLAUDE.md) — it routes by task type.

This graph is part of the broader [vCon ecosystem knowledge graph](https://github.com/vconic/vconic-docs). Updates to this file are hand-made when the spec kit's structure or the ecosystem repo list changes.
