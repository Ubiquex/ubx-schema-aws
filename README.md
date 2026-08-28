# ubx-schema-aws

A real, frozen, versioned AWS provider schema snapshot -- the pinnable
distribution artifact `ubx-provider-dynamic` and `ubiquex` resolve a
single `[providers.aws]` entry against, with zero network calls at
schema resolution time (see `provider/acquireschema.go` in `ubiquex`,
and `internal/snapshot`'s own doc comment in `ubx-provider-dynamic`).
The CloudFormation/Smithy split below is a real, internal
discovery-time detail -- one pin resolves all of it.

## What's here

AWS's own real published identity is a MIXED-SOURCE group of 430
members -- the only real group among this org's six real providers
whose own members span more than one real schema source (confirmed by
checking every real group's own member composition directly before
this repo was built):

- 1 real CloudFormation resource member (`aws`, the whole
  CloudFormation registry -- 1,715 real resource types -- fetched as
  one zip).
- 429 real Smithy data-source members (`aws_data_<service>`, one per
  real AWS service model, `github.com/aws/api-models-aws` --
  4,884 real data source types total across all of them).

- `manifest.json` -- the group's own real identity: `schema_format`,
  `provider`, one `version` for the WHOLE group, and which member names
  it bundles.
- `members/<name>.json` -- one real, complete, independently-diffable
  file per member. Committed as separate files, not one combined blob,
  so a real version bump's own git diff shows exactly which of the 430
  real members actually changed -- this is the whole reason the format
  exists at this scale: a single service's own model changing should
  never touch the other 429 files.
- `.github/workflows/hash-watch.yml` -- runs weekly (and on manual
  dispatch), regenerates every member from its own live source
  (CloudFormation's real registry zip for `aws`, each real service's
  own live Smithy model for every `aws_data_*` entry) and opens a PR
  only when the group's own mechanically-derived version (the highest
  real change level found across every member --
  `internal/snapshot`'s `AssembleGroup`) actually moves. Never
  auto-merges.
- `.github/workflows/publish.yml` -- manual-dispatch-only. Packs
  `manifest.json` and every `members/*.json` into one compressed
  archive (`snapshot.tar.gz`) and cuts a real GitHub Release tagged
  `v<version>` carrying exactly two assets: `snapshot.tar.gz` and
  `SHA256SUMS`. The archive exists purely so a real pinned resolution
  is still one real download regardless of member count -- the
  COMMITTED files (what a reviewer actually sees) are always the
  separate, per-member ones above.

## Serving a mixed-source group

Generating each member never needed to know about the others --
`GenerateCloudFormationMember`/`GenerateSmithyMember` are called per
member, dispatched by that member's own `schema_source`, exactly like
every single-source provider in this org. Only SERVING the group needed
new code: `ubx-provider-dynamic`'s `internal/mixedserver` package is a
thin `tfprotov6.ProviderServer` dispatch layer that routes each RPC to
whichever real sub-server (a `dynserver.Server` for CloudFormation-
sourced entries, `internal/smithy/server`'s own `Server` for
Smithy-sourced entries) actually owns the resource or data-source type
in question, since `GetProviderSchema`/`GetMetadata` are merged across
sources with the same collision discipline every same-source merge
already uses (a type owned by two sources fails loud, same as today).

## Consuming a real, published version

In `ubiquex`, one real pin resolves the whole group -- all 430 real
members, both sources, are served together from the SAME launch, the
SAME real download:

```toml
[providers.aws]
source  = "ubiquex/aws"
version = "1.0.0"
```

`provider.AcquireSchema`'s own cache-by-source+version resolves ONE real
download and ONE extracted cache directory
(`~/.ubx/schemas/ubiquex/aws/1.0.0/`) -- the launched process merges
every real member of the group into one served schema,
`ResourceSchemas` and `DataSourceSchemas` together, exactly like a
real, hand-written Terraform provider already looks from the outside.

## Versioning

One real, mechanically-derived semver number for the WHOLE group, not
one per member: the highest real change level found across every
member (a brand new resource type or a field that gained write access
bumps MINOR; a resource type or field that disappeared, or a field that
lost write access, bumps MAJOR; a pure description-text change bumps
PATCH), plus an unconditional MAJOR if a member the group used to
bundle is gone entirely. See `internal/snapshot/diff.go` and
`AssembleGroup` in `ubx-provider-dynamic` for the real rule.

`v1.0.0` is this group's real, first-ever snapshot, published once
UBI-193's own mixed-source dispatch layer made serving a real
CloudFormation+Smithy group possible -- generation itself never needed
that fix (`--generate-snapshot-group` already dispatched each member by
its own `schema_source` before the dispatch layer existed), only
serving a real pinned resolution did.

<!-- README-GEN:BEGIN -->
**Real, current published version:** `v1.0.0`

## Links

- Docs: https://docs.ubiquex.io
- Internals (architecture and design): https://github.com/Ubiquex/ubiquex-internals
- Linear board: https://linear.app/ubiquex
<!-- README-GEN:END -->
