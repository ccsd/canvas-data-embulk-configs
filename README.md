# canvas-data-embulk-configs
YAML configs for importing Canvas Data with [Embulk](https://www.embulk.org)

Essentially, these are provided as a starting point for _your own workflow_, to manage Canvas Data in YAML instead of in code.

Visit [Managing Canvas Data with Embulk](https://community.canvaslms.com/groups/canvas-developers/blog/2019/07/05/managing-canvas-data-with-embulk) on the CanvasLMS Community for discussions and workflow ideas.

![canvas data v4.2.5](https://img.shields.io/static/v1.svg?label=canvas%20data&message=v4.2.5&color=blue)

## Embulk
Embulk is an open-source bulk data loader that helps data transfer between various databases, storages, file formats, and cloud services. [https://www.embulk.org/docs/](https://www.embulk.org/docs/)

***with support for***
- [Linux, OSX, Windows](https://github.com/embulk/embulk#quick-start)
- [MySQL, MS SQL Server, Oracle, PostgreSQL, RedShift](https://github.com/embulk/embulk-output-jdbc)

***and features useful for Canvas Data***
- Decode gzipped files
- The ability to intelligently guess the format and data types of CSV files
- Parallel execution of tasks, multi-threading per CPU Core, and a task for each batch file
- Input CSV Plugin as default Input for Embulk
- Filter data with Filter Plugins, https://plugins.embulk.org/#filter
	- Add and Remove Columns... deprecated will always be NULL
    - Filter rows with SQL like syntax https://github.com/sonots/embulk-filter-row
    - Unique, Distinct, JOIN (CSV files)
- Output Data to SQL
    - Insert, Insert Direct, Replace, Merge, Truncate and Truncate Insert
    - Timestamp formatting
    - TimeZone conversion from UTC for date time columns before_load and after_load, config options to run queries before (truncate) and after import (indexes) and more


For more details see the [Wiki docs](https://github.com/ccsd/canvas-data-embulk-configs/wiki)

## Config files?

Embulk uses YAML config files for each task, for Canvas Data this means each input source (table files) and it's output destination (db table) is 1 file. This includes differences between staging, test and production destinations. 100 plus config files may seem like an odd workflow at first, but it's a lot less work to manage than generating DDLs with [schema.json](https://portal.inshosteddata.com/api/schema/latest), and plugins are a lot easier than coding custom sorting and filtering tasks.

Embulk can recreate the whole table each time the config is run. This means editing the config file is your only edit, leaving
- less importer coding
- less DDL scripting
- zero overrides

## Maintainers
I will attempt to keep these configs up-to-date, tagged with each schema version so you can use them in your own workflow. However, it's unlikely I'll be able to maintain and test the configs for 4 databases regularly, nor can I see the data affected by each institutions use case of CanvasLMS. You may see scenarios, data, and values others have not. I'm currently using MS SQL Server, and would appreciate anyone using these configs to help maintain as Canvas Data changes.

- Review the upcoming [Canvas Data Release Notes](https://community.canvaslms.com/community/answers/releases/release-notes-canvas-data)
- Review the schema documentation [Canvas Data Portal](https://portal.inshosteddata.com/docs)

## Contributing
If you use this repository, please consider submitting a [Pull Request](https://github.com/ccsd/canvas-data-embulk-configs/pulls) or [Issue](https://github.com/ccsd/canvas-data-embulk-configs/issues) for the following:
- If you experience issues with the CSV parsing parameters
- If you experience issues with getting data and rows into SQL with errors
- If you can improve the SQL
- If you find enumerables not listed
- If you find column lengths not accurate, or your own data is longer than defined
- We do not use Canvas Catalog, therefore the configs for these tables are not prepared and tested

> - The Oracle configs are currently only setup for `insert_method: normal` and not `oci`. OCI greatly improves the import speed. If you can help support this, please consider testing and documenting. https://github.com/embulk/embulk-output-jdbc/tree/master/embulk-output-oracle#insert-methods
> - Oracle has some compatibility and identifier length issues between versions, and I am currently not able to get the `after_load: indexes` working with my dev version, they do work directly in SQL editor.