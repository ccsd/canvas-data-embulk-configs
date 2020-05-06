# Change Log
All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](http://keepachangelog.com/)
and this project, and versions listed, match the versioning of [Canvas Data Portal](https://portal.inshosteddata.com/docs).

Changes refer to changes to the config files in support of the Canvas Data Schema

## [4.2.6] 2020-03-03
- assignment_dim.muted is now deprecated, will always be null
- assignment_dim.workflow_state found 2 new states (failed_to_import, failed_to_migrate), added to constraint, all databases

## [4.2.5] 2019-12-12
- Canvas released a non-breaking update to Canvas Data Portal documentation, no schema changes have been identified
- Adding new tag to match currently supported CD version

## [4.2.4] 2019-12-11
### Changed
- renamed `quiz_dim.body` to `name` per schema, for all databases

## [4.2.4] 2019-11-08
### Changed
- Updated `module_dim.name` length to 512 for all databases, after multiple institutions found values longer than previously defined

## [4.2.4] 2019-10-07
### Changed
- Added `fail_to_import` to ENUM constraint on assignment_dim.workflow_state, all databases
