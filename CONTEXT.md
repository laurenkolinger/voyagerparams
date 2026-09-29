# voyagerparams: glossary

Last updated: 2026-09-29 (LO)

This file is the vocabulary of the `voyagerparams` module: the words its code, UI, docs and log rows use for the things it handles, one meaning each. Agents read it before naming anything in this clone. The platform glossary at `/mnt/rip/vicarius_drive/CONTEXT.md` covers the terms every module shares, and `/mnt/rip/vicarius_drive/CONTEXT-MAP.md` indexes every glossary on the drive. Format rules for all of them are in `/mnt/rip/vicarius_drive/docs/agents/domain.md`.

## Language

**Ref**:
The short name a form field, a select box or a registry fact uses to name one parameter file: `default`, `v<MAJOR>.<MINOR>.<PATCH>`, `branches/<slug>` or `branches/<slug>/v<MAJOR>.<MINOR>.<PATCH>`, or `custom/<run_id>`. The library resolves a ref to a path under the phase folder and to a GitHub URL.

**Phase**:
One of the two parameter trees this checkout holds, `voyager1` or `voyager2`, each with its own `versions/`, `branches/`, `custom/` and `DEFAULT` pointer.

**Kind**:
Which of the three file categories a parameter file belongs to: a numbered default (`version`), a named variant (`branch`), or one run's exact set (`custom`). Recorded in the file's header and implied by the ref that names it.

**DEFAULT pointer**:
The one-line file at the root of a phase folder, for example `voyager1/DEFAULT`, naming the version ref that the ref `default` resolves to. Rewritten only when a new default version is saved.

**Version bump**:
How a phase's default version number changes on save: MINOR when only values changed, MAJOR when a key was added, removed or renamed, and PATCH always 0, because this module has no polish save. This is stricter than the platform's general `v{MAJOR}.{MINOR}.{PATCH}` naming law: a voyagerparams version never bumps on PATCH alone.
_Avoid_: patch release (this module never cuts one)

**Branch**:
A named variant of a phase's parameters, started from a base ref (normally the default) and versioned within itself by the same MINOR and MAJOR rule as a default save. A branch never becomes the phase default on its own; only a Save as default in the Atlas control panel promotes its values into a new numbered version.

**Custom set**:
The exact parameter file one Voyager run used, saved as `custom/<run_id>.yaml` when a person runs with edited or imported parameters instead of picking a named version. Its header carries the `params_version` of the file it started from, not a version of its own, and it never becomes a default or a branch. The Voyager 1 setup page always saves a custom set with overwrite enabled, and a run id carries no time component: only the run date, the initials, each site's transect count, and the earliest and latest timepoints in the selection. A second run that reduces to the same run id replaces the earlier custom file rather than being refused, even when its transects are not the same ones.
_Avoid_: run config

**Base ref**:
The ref a branch's first file or a custom set started from, recorded in that file's header as `base_ref`. Names where the values came from, not where they are going.

**Header**:
The leading `# key: value` comment lines every parameter file opens with (`params_version`, `phase`, `kind`, `status`, `branch`, `base_ref`, `run_id`, `description`, `why`, `saved_at`, `saved_by`). The library reads these lines; Voyager 1 and Voyager 2 ignore them when they load the file as `analysis_params.yaml`.
