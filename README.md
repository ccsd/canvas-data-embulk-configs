# canvas-data-embulk-configs
YAML configs for importing Canvas Data with [Embulk](https://www.embulk.org)

Essentially, these are provided as a starting point for _your own workflow_, to manage Canvas Data in YAML instead of in code.

Visit [Managing Canvas Data with Embulk](https://community.canvaslms.com/groups/canvas-developers/blog/2019/06/26/managing-canvas-data-with-embulk) on the CanvasLMS Community for discussions and workflow ideas.


## Embulk
Embulk is a parallel pluggable open-source data loader that can transfer data between various sources, file formats and services.
[https://www.embulk.org/docs/](https://www.embulk.org/docs/)

It's easy to [install and manage](https://github.com/embulk/embulk#linux--mac--bsd)

It's very well suited for Canvas Data, with support for popular databases.

Input and Output Plugins make ETL easy.
- ___CSV to SQL___
- CSV to CSV
- [SQL to SQL](https://github.com/ccsd/canvas-data-embulk-configs/wiki/SQL-in-SQL-out)

Typical usage for Embulk would be CSV in SQL out

SQL Plugins
- [Embulk Output JDBC](https://github.com/embulk/embulk-output-jdbc) (Data into SQL)
- [Embulk Input JDBC](https://github.com/embulk/embulk-input-jdbc) (Data from SQL)

### Filter Plugins
see wiki: [Filters & Examples](https://github.com/ccsd/canvas-data-embulk-configs/wiki/Filters-&-Examples) for Canvas Data

[more plugins](https://plugins.embulk.org/)


### Additional Features

- [Decodes GZIP](https://www.embulk.org/docs/built-in.html#gzip-decoder-plugin) (no unpacking necessary)

- [Variables and Templates](https://www.embulk.org/docs/built-in.html#id39)

- Default TimeZone: (see SQL Output docs) If the sql type of a column is date/time/datetime and the embulk type is string, column values are formatted int this default_timezone. You can overwrite timezone for each columns using column_options option. (string, default: UTC)



## Why config files?

Since Embulk uses plugins and each input and output source can be different, it makes sense that each Canvas Data table will have it's own config file. This might seem odd at first, not being a single application that imports all your data into the database.

However, let's consider some of the biggest pain points in managing Canvas Data as it is currently provided.
Developers like to generate DDLs of the schema, either manually with the documentation or dynamically using [schema.json](https://portal.inshosteddata.com/api/schema/latest)
	
This is problematic, because the data defined in the docs and JSON are not complete.
- Enumerables are missing
- Field Lengths are not always accurate
 
This means, that any time Canvas updates Canvas Data with new tables, new columns, deprecated columns you might have to manually ALTER TABLE, update the JSON to DDL generator, and evaluate any new 'overrides' necessary to correct the process...

eg: ([Canvas Data SDK overrides](https://github.com/Harvard-University-iCommons/canvas-data-sdk/blob/master/canvas_data/ddl_utils.py#L49), [Canvancement overrides](https://github.com/jamesjonesmath/canvancement/blob/master/canvas-data/php/overrides.json))

_This is tech debt insanity_
I love these tools, I love the effort.

But the former process left me updating and repairing schema and import tools far more often than necessary, when what I want to do is *use* the data and write new tools for our staff.

These config files, which I will attempt to keep up-to-date, tagged with each schema version can be downloaded by you, and you can mix and manage your own workflow into these configs. You can review the upcoming [Canvas Data Release Notes](https://community.canvaslms.com/community/answers/releases/release-notes-canvas-data) and be prepared for the rollover without a whole mess of alter tables and overrides to consider.

Embulk can recreate the whole table each time the config is run. This means editing the config file is your only edit, leaving
- less importer coding
- less DDL scripting
- less ALTER TABLE statements
- zero overrides

### SQL Config

Read the documentation for each database plugin for more details.

Multiple import modes are available, including Insert, Insert Direct, Replace, Merge, Truncate and Truncate Insert.

- I use *replace* for every table except Requests where I use Insert.
- You can run queries `before_load` and `after_load`.
- After Load is where I have provided examples of Enumerables and Indexes*
 
*based on documentation plus successfully importing our data.


## Contributing
If you use this repository, please consider submitting Pull Requests to keep this resource up to date.
- If you expereince issues with the CSV parsing parameters
- If you experience issues with getting data and rows into SQL with errors, evaluate the issue and update the config
- If you can improve the SQL, please share.
- If you find you have ENUMERABLES not listed, please update the table config.
- If you find column lengths not accurate please update with the MAX number you've seen.

### Maintainers
I cannot obviously continously maintain and test these for 4 databases. I'm currently using MS SQL Server, and would appreciate anyone  using this to help maintain as Canvas Data changes.

### Known Issues
I have tested data import for MySQL, MSSQL, Postgres, and Oracle.
I can get our data into all of these databases.
However, Oracle has some compatability and identifier length issues between versions, and I am currently not able to get the ```after_load: indexes``` working. It would be very helpful if you can contribute fixes for these. The Oracle configs are currently only setup for 'normal' insert and not 'oci'.