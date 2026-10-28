# java-sdk patches

Patch files in this directory apply to the Global Payments Java SDK:

- Source: <https://github.com/globalpayments/java-sdk>
- Local working clone: `D:\Projects\SDK\java-sdk`
- Default upstream base branch: `master`

Follow the repository-wide [patch workflow](../PATCH_WORKFLOW.md). Patch names
must match their filesystem-safe source branch name. An ordered numeric suffix
may be added when a change requires multiple patches.

## Available patches

### `fix-portico-lodging-data.patch`

- Source branch: `fix-portico-lodging-data`
- Upstream base: `origin/master` at `38065b8437e5`
- Source commit: `c6ad61668779`
- Change: restores Portico authorization-request lodging data serialization and
  corrects the check-in/check-out date format to `yyyy-MM-dd`.
- Validation: Maven compilation passed. Five representative existing
  `LodgingCertification` tests generated the expected lodging XML but the live
  certification endpoint rejected the repository's embedded credentials with
  `Authentication Error (-2)`.
