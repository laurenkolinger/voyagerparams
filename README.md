# voyagerparams

Last updated: 2026-09-29 (LO)

- Tags: 3D | Version: 0.1.0 | Status: active resource | Owner: Lauren Olinger
- Repo: `laurenkolinger/voyagerparams` | Related studies: none (an administrative resource, not scoped to one study)

## What it does and why it exists

voyagerparams is a resource module: the local checkout of the public repository `laurenkolinger/voyagerparams`, which holds every version of the Voyager 1 and Voyager 2 analysis parameters as YAML files. The checkout is `/mnt/rip/vicarius_drive/vicarius/modules/voyagerparams/github_repo/` (this folder). It has no code of its own; every reader and writer goes through the library `/mnt/rip/vicarius_drive/vicarius/modules/voyager_tools/github_repo/voyagertools/paramsstore.py`.

Voyager 1 copies a parameter template into each processing folder and Metashape reads the values from there. Before this repository existed, the template had no version, so a row in the TCRMP 3D atlas could not say which parameters built it. Now every run points at one file here, a changed default is a new numbered file rather than an edit to the old one, and the exact set a run used is saved under its run id.

The Voyager 1 setup page loads a named version and, when a person edits or imports parameters there instead of picking one, saves that exact set as a custom run record. The Voyager 2 setup page loads a named version for the `voyager2` phase and has no edit or import mode yet. The Atlas control panel (`voyager_tools`) writes new default versions and branches for both phases.

## Where it sits in the pipeline

voyagerparams sits beside the survey chain (`3D_phase_1 -> manual_edit -> 3D_phase2 -> tagfab`), not inside it: it holds the analysis parameters `3D_phase_1` (Voyager 1) and `3D_phase2` (Voyager 2) use. Each setup page picks a file through `paramsstore.py`, and the run then reads that file from this checkout by its path. It has two writers: the Atlas control panel (`voyager_tools`, at `http://127.0.0.1:5090/voyager-tools`), which saves new default versions and branches, and the Voyager 1 setup page itself, which saves a custom run record automatically when a person edits or imports parameters before pressing RUN.

Downstream, the atlas (module `tcrmp_3d_atlas`, desktop tile ATLAS, `http://127.0.0.1:5090/atlas`) shows which file a run used: a row's Voyager 1 or Voyager 2 section carries the `params_version` and a link built from `params_url`, both written into the registry's row facts at run time. The atlas never reads or writes this checkout; it only displays the facts Voyager 1 and Voyager 2 already recorded, so the version and link it shows point at a real file here. This is a different screen from the Atlas control panel: the Atlas control panel writes new parameter files, and the atlas shows which one a run already used.

## Inputs

`module.yaml` declares `inputs: []`. voyagerparams is a resource with no run inputs of its own: nothing is uploaded or selected here, because there is no run. The three screens that read and write the files in this checkout are named under How to run it below.

## Parameters

`module.yaml` declares no `parameters:` block: voyagerparams is not a launched pipeline stage with a form of its own. The values this checkout stores are the Voyager 1 and Voyager 2 analysis parameters themselves, data this module holds rather than a setting a person picks to run it. Their meaning is in the editor schemas `/mnt/rip/vicarius_drive/vicarius/modules/voyager_tools/github_repo/voyagertools/params_schema_voyager1.yaml` and `params_schema_voyager2.yaml` beside it, and, for Voyager 1, in the "Parameters" section of `/mnt/rip/vicarius_drive/vicarius/modules/3D_phase_1/github_repo/README.md`.

## Outputs and data-dictionary entries

`module.yaml` declares `outputs: []`, and this module owns no data-dictionary descriptor. The YAML files here are inputs Voyager 1 and Voyager 2 consume, not catalogued datasets voyagerparams itself produces.

## How to run it

A resource module has no launch and no run folder. This section names the three screens that read and write the files here, where admin mode is entered, where voyagerparams's own tile stands, and states that no command line reaches these files.

### From the UI

Open `http://127.0.0.1:5090` (the VICARIUS desktop). Three screens read and write the files in this checkout, and none of them is voyagerparams's own tile:

- The Voyager 1 setup page, at `http://127.0.0.1:5090/voyager1`, opens from the desktop tile VOYAGER 1 (module `3D_phase_1`). It loads a named `voyager1/` version, and when a person edits or imports parameters there instead of picking one, saves that exact set as a custom run record.
- The Voyager 2 setup page, at `http://127.0.0.1:5090/voyager2`, opens from the desktop tile VOYAGER 2 (module `3D_phase2`). It loads a named `voyager2/` version and has no edit or import mode yet.
- The Atlas control panel, at `http://127.0.0.1:5090/voyager-tools` (module `voyager_tools`), opens from the item ATLAS CONTROL PANEL in the desktop's DEV folder, from the "Open the Atlas control panel" button in the admin window, or from the link in the atlas row view; all three show only in admin mode. The panel writes new default versions and branches for both phases.

Admin mode is entered from the desktop's own top bar, not from this module: the chip beside the clock reads "locked" until someone clicks it, enters the admin PIN in the window that opens, and confirms it. VICARIUS then shows every admin-only tile, including the ATLAS CONTROL PANEL item, until ten minutes pass with no further admin action, at which point it locks again.

A row's Voyager 1 or Voyager 2 section in the atlas (module `tcrmp_3d_atlas`, desktop tile ATLAS, `http://127.0.0.1:5090/atlas`) shows the `params_version` and a link to the file a run used, without opening any of the three screens above; see Where it sits in the pipeline.

### The voyagerparams tile

voyagerparams ships its own `module.yaml` (`/mnt/rip/vicarius_drive/vicarius/modules/voyagerparams/github_repo/module.yaml`) and registers like any other module: `/modules/voyagerparams` resolves and the desktop discovers it. Its `hidden: true` flag keeps it off the default `/modules` grid and off `vicarius list`, because there is nothing to launch here, only the YAML files the three screens above read and write. The `SHOW HIDDEN` chip, `?show_hidden=1`, or `--hidden` reveal it, per `/mnt/rip/vicarius_drive/vicarius/_DOCS/reference/MODULE_REGISTRY_GUIDE.md`.

### From the CLI

No command-line entry point exists for this module or for the library behind it. `paramsstore.py`, at `/mnt/rip/vicarius_drive/vicarius/modules/voyager_tools/github_repo/voyagertools/paramsstore.py`, is a plain Python library: it defines no `argparse` parser and has no `__main__` block. Writing a new version, branch or custom set outside the three screens above means importing `ParamsStore` from that file and calling its methods (`save_default`, `save_branch`, `save_custom`) from a Python session or script; no packaged CLI wraps it.

## How it works inside

### Layout

```
voyager1/
  DEFAULT                    one line: the version that is the default, for example v1.0.0
  versions/v1.0.0.yaml       numbered defaults; the newest one is normally DEFAULT
  branches/<slug>/v1.0.0.yaml   named variants that never become the default by themselves
  custom/<run_id>.yaml       the exact set one run used, written when RUN is pressed
voyager2/
  DEFAULT, versions/, branches/, custom/   the same shape for Voyager 2; the current default (v0.1.0) is still a draft
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

### The versioning rule

This is the rule from section 7.7 of the program spec at `/mnt/rip/vicarius_drive/docs/superpowers/specs/2026-09-03-voyager-program-design.md`, in plain words. Versions are `v<MAJOR>.<MINOR>.<PATCH>`.

1. Saving a new default compares the new values with the current default. When only values changed, the MINOR number goes up (v1.0.0 to v1.1.0). When a key was added, removed or renamed, the MAJOR number goes up (v1.1.0 to v2.0.0). PATCH stays 0; there is no polish save. A save that changes nothing is refused.
2. The new file is written to `versions/v<next>.yaml` and `DEFAULT` is rewritten to name it. Older versions are never edited or deleted, so a row that points at v1.0.0 keeps pointing at the same values forever.
3. A branch is a named variant. Its first file takes the version of the file it started from (normally the default) and carries the branch name in its header; later saves to the same branch bump within the branch by the same MINOR and MAJOR rule. A branch never becomes the default on its own; making it the default is a Save as default in the Atlas control panel, which produces a new numbered version.
4. A custom set is the exact parameter file one run used. The Voyager 1 setup page saves it as `custom/<run_id>.yaml` only when RUN is pressed with edited or imported parameters; picking an existing version by ref keeps that version as the run's record and writes no custom file. Its header carries the `params_version` of the file it started from rather than a version of its own, and it never becomes a branch or the default by itself. `save_custom` in `paramsstore.py` defaults to `overwrite=False`; the Voyager 1 setup page always calls it with `overwrite=True`. A run id carries no time component: only the run date, the initials, each selected site's transect count, and the earliest and latest timepoints in the selection. Two selections that agree on those parts reduce to the same run id even when the transects themselves differ, and a later save then replaces the earlier custom file rather than being refused.
5. Every save in the Atlas control panel commits and pushes to this public repository, so the file the atlas links to is the file on GitHub. The custom set of a run is committed the same way.
6. Voyager 2 keys live in the same repository under `voyager2/` and follow the same versioning rule. VOYAGER 2, the module `3D_phase2`, has its own setup page. That page reads a chosen `voyager2/` version through this library, but only accepts a file whose top level carries a `voyager2:` mapping, the shape `/mnt/rip/vicarius_drive/vicarius/modules/voyager_tools/github_repo/voyagertools/params_schema_voyager2.yaml` validates. `voyager2/versions/v0.1.0.yaml` is a draft capture of the legacy `3D_phase2` keys, taken before that rewrite and marked `status: draft` in its header. It carries no top-level `voyager2:` key, so the Voyager 2 setup page marks it unusable and falls back to VOYAGER 2's own defaults at `/mnt/rip/vicarius_drive/vicarius/modules/3D_phase2/github_repo/config/voyager2_defaults.yaml`.

### How VICARIUS refers to a file

A ref is the short name a select or a fact uses: `v1.0.0` for a numbered default, `branches/<slug>/v1.0.0` for a branch file (or `branches/<slug>` for the newest file in that branch), `custom/<run_id>` for a run's set, and `default` for whatever `DEFAULT` names. The library resolves a ref to a path under the phase folder and to a URL of the form `https://github.com/laurenkolinger/voyagerparams/blob/main/voyager1/versions/v1.0.0.yaml`, which Voyager 1 writes into the row's facts as `params_url` beside `params_version`.

### Where the first two files came from

- `voyager1/versions/v1.0.0.yaml` is the Voyager 1 template `/mnt/rip/vicarius_drive/vicarius/modules/3D_phase_1/github_repo/analysis_params.yaml` as shipped by module 3D_phase_1 v1.1.0, unchanged apart from the header. The meaning of every key is in that module's manual, `/mnt/rip/vicarius_drive/vicarius/modules/3D_phase_1/github_repo/README.md`, section "Parameters", and in the editor schema `/mnt/rip/vicarius_drive/vicarius/modules/voyager_tools/github_repo/voyagertools/params_schema_voyager1.yaml`.
- `voyager2/versions/v0.1.0.yaml` captured the legacy `3D_phase2` parameter file as it stood on 2026-09-04, marked draft, with the Sketchfab API token blanked (the repository is public; a token is supplied at run time and never committed) and the `chunk_quality` block indented under its parent so it parses as the mapping the comments describe. `3D_phase2` has since replaced that file with `/mnt/rip/vicarius_drive/vicarius/modules/3D_phase2/github_repo/config/voyager2_defaults.yaml`; the original path no longer exists in that clone. The meaning of the keys VOYAGER 2 actually reads, the `voyager2:` block, is in the editor schema `/mnt/rip/vicarius_drive/vicarius/modules/voyager_tools/github_repo/voyagertools/params_schema_voyager2.yaml`.

## Gotchas and troubleshooting

- Public repository: nothing secret goes in here (no tokens, passwords, hostnames with credentials, or NAS paths that reveal more than a folder name), and no data file, only parameter YAML, this README, the changelog and the module contract.
- Files here are written by the library, not by hand. A hand edit of a numbered version breaks rule 2 above; make a new version instead.
- A custom run set is silently replaced, not refused, when a second run reduces to the same run id: same run date, same initials, same site codes each with the same transect count, and the same earliest and latest timepoints (rule 4 above). Two different transect selections can still share all of that and collide, so only a different run date or different initials is guaranteed to keep an earlier custom set on record.
- `python3 tests/test_house_rules.py` from this folder checks every Markdown file against the prose rules in `/mnt/rip/vicarius_drive/CLAUDE.md`; it does not touch the parameter files.

## Resume from cold

The checkout is `/mnt/rip/vicarius_drive/vicarius/modules/voyagerparams/github_repo/`, a plain git clone with the remote `laurenkolinger/voyagerparams` (public). Nothing needs installing to read it: the files are YAML and Markdown, and the library that writes them, `/mnt/rip/vicarius_drive/vicarius/modules/voyager_tools/github_repo/voyagertools/paramsstore.py`, ships inside the `voyager_tools` clone with its own dependency, `pyyaml`.

A save has committed and pushed successfully when `git -C /mnt/rip/vicarius_drive/vicarius/modules/voyagerparams/github_repo log -1 --format='%H %s'` shows the new commit, `git -C /mnt/rip/vicarius_drive/vicarius/modules/voyagerparams/github_repo status --short` reports a clean tree, and the phase's `DEFAULT` file (`voyager1/DEFAULT` or `voyager2/DEFAULT`) names the new version. The Atlas control panel and the Voyager setup pages report a push failure in their own response instead of raising, so a save can commit locally and still need a manual `git -C /mnt/rip/vicarius_drive/vicarius/modules/voyagerparams/github_repo push origin main` if the network was down at save time.

Verifying this checkout is its whole test suite: `python3 tests/test_house_rules.py` from this folder scans every Markdown file for the house-rule offences named in Gotchas above and prints `PASS` or the offending lines. The library's own behaviour (versioning, locking, git commit and push) is tested from the `voyager_tools` clone instead: `/usr/bin/python3 -m unittest discover -s tests -v` from `/mnt/rip/vicarius_drive/vicarius/modules/voyager_tools/github_repo`, which runs `tests/test_paramsstore.py` against a temporary checkout and a fake git runner, never the real one here.

## Provenance and links

- Program spec: `/mnt/rip/vicarius_drive/docs/superpowers/specs/2026-09-03-voyager-program-design.md` (sections 5.3 and 7.7)
- Build plan: `/mnt/rip/vicarius_drive/docs/superpowers/plans/2026-09-03-voyager-program-build.md` (task A7)
- Rules for agents: `/mnt/rip/vicarius_drive/CLAUDE.md`; this clone's pointer is `CLAUDE.md` beside this file
- Module registry guide: `/mnt/rip/vicarius_drive/vicarius/_DOCS/reference/MODULE_REGISTRY_GUIDE.md`
- The library and its module: `/mnt/rip/vicarius_drive/vicarius/modules/voyager_tools/github_repo/voyagertools/paramsstore.py`, manual at `/mnt/rip/vicarius_drive/vicarius/modules/voyager_tools/github_repo/README.md`
- This module owns no data-dictionary descriptor.
