# voyagerparams

Last updated: 2026-09-04 (LO)

Every version of the Voyager 1 and Voyager 2 analysis parameters, kept as YAML files in the public repository `laurenkolinger/voyagerparams`. The local checkout is `/mnt/rip/vicarius_drive/vicarius/modules/voyagerparams/github_repo/` (this folder). It is a resource module with no code of its own: the Voyager 1 setup page reads it, Voyager tools writes it, and both go through the library `/mnt/rip/vicarius_drive/vicarius/modules/voyager_tools/github_repo/voyagertools/paramsstore.py`.

## Why it exists

Voyager 1 copies a parameter template into each processing folder and Metashape reads the values from there. Before this repository the template had no version, so a row in the atlas could not say which parameters built it. Now every run points at one file here (the atlas shows the version and links to it), a changed default is a new numbered file rather than an edit, and the exact set a run used is saved under its run id.

## Layout

```
voyager1/
  DEFAULT                    one line: the version that is the default, for example v1.0.0
  versions/v1.0.0.yaml       numbered defaults; the newest one is normally DEFAULT
  branches/<slug>/v1.0.0.yaml   named variants that never become the default by themselves
  custom/<run_id>.yaml       the exact set one run used, written when RUN is pressed
voyager2/
  DEFAULT, versions/, branches/, custom/   the same shape for Voyager 2 (draft until Voyager 2 is built)
CHANGELOG.md                 one line per change to the files here, newest first
```

Every parameter file opens with a comment header that the library reads and the module ignores:

```
# params_version: 1.0.0
# phase: voyager1
# kind: version            (version, branch or custom)
# description: ...
# saved_at: 2026-09-04T00:29:31-04:00
# saved_by: LO
```

A branch file also carries `# branch: <slug>`, `# base_ref: <ref>` and `# why: ...`; a custom file carries `# run_id: <run id>`; a draft carries `# status: draft`. The rest of the file is the `analysis_params.yaml` the module consumes, with its per-key comments kept, so the file on GitHub reads like the template does.

## The versioning rule

This is the rule from section 7.7 of the program spec at `/mnt/rip/vicarius_drive/docs/superpowers/specs/2026-09-03-voyager-program-design.md`, in plain words. Versions are `v<MAJOR>.<MINOR>.<PATCH>`.

1. Saving a new default compares the new values with the current default. When only values changed, the MINOR number goes up (v1.0.0 to v1.1.0). When a key was added, removed or renamed, the MAJOR number goes up (v1.1.0 to v2.0.0). PATCH stays 0; there is no polish save. A save that changes nothing is refused.
2. The new file is written to `versions/v<next>.yaml` and `DEFAULT` is rewritten to name it. Older versions are never edited or deleted, so a row that points at v1.0.0 keeps pointing at the same values forever.
3. A branch is a named variant. Its first file takes the version of the file it started from (normally the default) and carries the branch name in its header; later saves to the same branch bump within the branch by the same MINOR and MAJOR rule. A branch never becomes the default on its own; making it the default is a Save as default in Voyager tools, which produces a new numbered version.
4. A custom set is the exact parameter file one run used, saved as `custom/<run_id>.yaml` when RUN is pressed on the Voyager 1 setup page. It carries no version number of its own, is never edited afterwards, and never becomes the default by itself.
5. Every save in Voyager tools commits and pushes to this public repository, so the file the atlas links to is the file on GitHub. The custom set of a run is committed the same way.
6. Voyager 2 keys live in the same repository under `voyager2/` and follow the same rule. Until the Voyager 2 build ships, `voyager2/versions/v0.1.0.yaml` is a draft capture of the legacy keys and is marked so in its header.

## How VICARIUS refers to a file

A ref is the short name a select or a fact uses: `v1.0.0` for a numbered default, `branches/<slug>/v1.0.0` for a branch file (or `branches/<slug>` for the newest file in that branch), `custom/<run_id>` for a run's set, and `default` for whatever `DEFAULT` names. The library resolves a ref to a path under the phase folder and to a URL of the form `https://github.com/laurenkolinger/voyagerparams/blob/main/voyager1/versions/v1.0.0.yaml`, which Voyager 1 writes into the row's facts as `params_url` beside `params_version`.

## Where the first two files came from

- `voyager1/versions/v1.0.0.yaml` is the Voyager 1 template `/mnt/rip/vicarius_drive/vicarius/modules/3D_phase_1/github_repo/analysis_params.yaml` as shipped by module 3D_phase_1 v1.1.0, unchanged apart from the header. The meaning of every key is in that module's manual, `/mnt/rip/vicarius_drive/vicarius/modules/3D_phase_1/github_repo/README.md`, section "Parameters", and in the editor schema `/mnt/rip/vicarius_drive/vicarius/modules/voyager_tools/github_repo/voyagertools/params_schema_voyager1.yaml`.
- `voyager2/versions/v0.1.0.yaml` is the legacy file `/mnt/rip/vicarius_drive/vicarius/modules/3D_phase2/github_repo/analysis_params.yaml`, marked draft, with the Sketchfab API token blanked (the repository is public; a token is supplied at run time and never committed) and the `chunk_quality` block indented under its parent so it parses as the mapping the comments describe.

## Rules for this repository

- Public. Nothing secret goes in: no tokens, passwords, hostnames with credentials, or NAS paths that reveal more than a folder name.
- Data files never go in: only parameter YAML, this README, the changelog and the module contract.
- Files are written by the library, not by hand. A hand edit of a numbered version breaks the promise in rule 2; make a new version instead.
- Prose here follows the platform rules in `/mnt/rip/vicarius_drive/CLAUDE.md`: no emoji, no em or en dashes, Atlantic Standard Time timestamps, actions signed LO. `python3 tests/test_house_rules.py` from this folder checks every Markdown file.

## Related documentation

- Program spec: `/mnt/rip/vicarius_drive/docs/superpowers/specs/2026-09-03-voyager-program-design.md` (sections 5.3 and 7.7)
- Build plan: `/mnt/rip/vicarius_drive/docs/superpowers/plans/2026-09-03-voyager-program-build.md` (task A7)
- Rules for agents: `/mnt/rip/vicarius_drive/CLAUDE.md`; this clone's pointer is `CLAUDE.md` beside this file
- Module registry guide: `/mnt/rip/vicarius_drive/vicarius/_DOCS/reference/MODULE_REGISTRY_GUIDE.md`
