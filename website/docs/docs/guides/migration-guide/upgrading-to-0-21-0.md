---
title: "Upgrading to 0.21.0"

---

### Resources

- [Discourse](https://discourse.getdbt.com/t/3077)
- [Release notes](https://github.com/dbt-labs/dbt-core/releases/tag/v0.21.0)
- [Full changelog](https://github.com/fishtown-analytics/dbt/blob/0.21.latest/CHANGELOG.md)

## Breaking changes

- `dbt source snapshot-freshness` has been renamed to `dbt source freshness`. Its node selection logic is now consistent with other tasks. In order to check freshness for a specific source, you must prefix it with `source:`.
- **Snowflake:** Turn off transactions and turn on autocommit by default. Explicitly specify `begin` and `commit` for [DML statements](https://stackoverflow.com/a/44796508) in incremental and snapshot materializations. Note that this may affect user-space code that depends on transactions.
- **Artifacts:**
    - [`manifest.json`](manifest-json) uses a `v3` schema that includes additional node properties (no changes to existing properties)
    - [`run_results.json`](run-results-json) uses a `v3` schema that has added `skipped` as a potential `TestResult`
    - [`sources.json`](sources-json) has a new `v2` schema that has added timing and thread details

## New and changed documentation

### Tasks
- [Commands](dbt-commands), [`build`](commands/build), [rpc](rpc): Add `dbt build`
- [Commands: `source`](commands/source): Renamed to `dbt source freshness`.
- [`deps`](commands/deps): Add `dbt deps` logging for outdated packages
- [`list`](commands/list): Add `--output-keys` flag and RPC parameter

## Selection
- [Commands: `source`](commands/source): Updated selection logic to match other tasks. When selecting a specific source to check freshness, you must prefix it with `source:`.
- [Node selection syntax](node-selection/syntax), [commands](dbt-commands): Switch `--models` for `--select` across the board. (Commands which previously used the `--models` flag still support it for backwards compatibility.)
- [YAML selectors](yaml-selectors#default) now support an optional `default` property. If set, dbt will use custom selection criteria for commands that do not specify their own selection/exclusion flags.
- [Selection methods](node-selection/methods) and [state comparison caveats](state-comparison-caveats): Add `state:modified` subselectors, and reflect that it now includes changes to upstream macros.
- [Test selection examples](test-selection-examples) includes more discussion of indirect selection (a change in v0.20), and the optional "greedy" flag/property (new in v0.21), which you can optionally set to include tests that have a mix of selected + unselected parents

### Elsewhere in Core
- [Resource configs and properties](configs-and-properties) docs have been consolidated and reconciled. New `config` property that makes it possible to configure models, seeds, snapshots, and tests in all yaml files.
- [Configuring incremental models](configuring-incremental-models): New optional configuration for incremental models, `on_schema_change`.
- [Environment variables](env_var): Add a log-scrubbing prefix, `DBT_ENV_SECRET_`
- [Test `where` config](where) has been reimplemented as a macro (`get_where_subquery`) that you can optionally reimplement, too
- [`dispatch`](dispatch) now supports reimplementing global macros residing in the `dbt` macro namespace with versions from installed packages, by leveraging `search_order` in the [`dispatch` project config](project-configs/dispatch-config)

### Plugins
- **Postgres** [profile](postgres-profile) property `connect_timeout` now configurable. Also applicable to child plugins (e.g. `dbt-redshift`)
- **Redshift**: [profile](redshift-profile) property `ra3: true` to support cross-database source definitions and read-only querying
- **BigQuery**: [profile](bigquery-profile) property `execution_project` now configurable. [Snapshots](snapshots) support `target_project` and `target_dataset` config aliases.
