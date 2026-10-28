# SDK patch transfer workflow

Use this repository to transfer reviewed source changes between development
environments when the source repository itself cannot be shared directly.

## Repository layout

Keep each set of patches under a directory named for its source SDK repository:

```text
SDK/
|-- java-sdk/                 # Working clone of the public source repository
`-- data-daemon/              # Transfer repository
    |-- PATCH_WORKFLOW.md
    `-- java-sdk/             # Patches that apply to java-sdk
        |-- README.md
        `-- <branch-name>.patch
```

Create a new top-level directory under `data-daemon` when work begins on a
different SDK. The directory name should match the source repository name, such
as `dotnet-sdk`, `java-sdk`, or `php-sdk`.

## Patch naming convention

Use a filesystem-safe branch name and give its patch the same name. For example,
branch `fix-portico-lodging-data` produces:

```text
fix-portico-lodging-data.patch
```

If a change genuinely requires a multi-commit series, append the four-digit
sequence emitted by `git format-patch`, such as
`fix-portico-lodging-data-0001.patch`. Prefer one focused commit and one patch
for routine fixes.

## Create and stage patches

1. In the source SDK clone, update the intended base branch and create a
   dedicated work branch.
2. Make the changes, run the relevant tests, review the diff, and commit the
   work. Each commit should be independently understandable.
3. Generate a patch series relative to the base branch. From the source SDK
   repository, for example:

   ```powershell
   git format-patch origin/master..HEAD --output-directory ..\data-daemon\java-sdk
   ```

4. Rename the generated file to match the source branch name. Do not modify the
   contents of a generated patch by hand.
5. Inspect every patch before publishing it. A patch contains commit metadata,
   messages, and source diffs; remove the patch and amend the source commit if it
   includes credentials, internal URLs, customer data, proprietary code, or
   other material that must not enter this public repository.
6. In `data-daemon`, commit the patch files and push them to the personal remote.

Record the source repository, source branch, intended base branch, and any
special application or test instructions in the SDK directory's `README.md`.

## Apply patches on the destination machine

1. Pull the latest `data-daemon` changes.
2. In the destination SDK clone, check out the correct internal base branch and
   create the intended internal work branch.
3. Apply a single patch or an ordered patch series with `git am`:

   ```powershell
   $patches = Get-ChildItem D:\path\to\data-daemon\java-sdk\<branch-name>*.patch |
       Sort-Object Name |
       Select-Object -ExpandProperty FullName
   git am $patches
   ```

4. Resolve conflicts in the destination repository if necessary, then continue
   with `git am --continue`. Use `git am --abort` to return to the pre-apply
   state if the series cannot be safely applied.
5. Review the resulting commits and run the destination repository's required
   build and tests before pushing to the internal remote.

## Guardrails

- Treat `data-daemon` as public, even if its visibility changes later.
- Transfer source patches only. Do not add credentials, configuration secrets,
  customer data, build output, dependency caches, or binaries.
- Generate patches from committed changes so authorship and commit boundaries
  are preserved.
- Keep patches grouped under the SDK directory they apply to.
- Do not reuse or overwrite a published patch. Add a corrected patch series with
  a new commit instead.
