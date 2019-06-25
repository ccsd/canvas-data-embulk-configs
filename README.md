# canvas-data-embulk-configs
Example YAML configs for importing Canvas Data with Embulk

Essentially, these are provided as a starting point for _your own workflow_, to manage Canvas Data in YAML instead of in code.



## Embulk
Embulk is a parallel pluggable open-source data loader that can transfer data between various sources, file formats and services.
[https://www.embulk.org/docs/](https://www.embulk.org/docs/)

It's easy to [install and manage](https://github.com/embulk/embulk#linux--mac--bsd)

It's very well suited for Canvas Data.
with support for popular databases like MySQL, SQL Server, Postgres, Oracle, RedShift

Input and Output Plugins make ETL easy.
	CSV to SQL
    CSV to CSV
    SQL to SQL

A basic Embulk CD task would be CSV to SQL out

https://github.com/embulk/embulk-output-jdbc

### Filters
[https://plugins.embulk.org/#filter](https://plugins.embulk.org/#filter)

My favorites, especially suited for Canvas Data management

[___embulk-filter-column___](https://github.com/sonots/embulk-filter-column)

- allows you to drop and add columns
- _great for removing deprecated fields_
- _also great for table that have no PK or id column_

[___embulk-filter-row___](https://github.com/sonots/embulk-filter-row)
- allows you to ignore and filter rows with SQL like syntax
- _great for ignoring rows from last school year_

[___embulk-filter-unique___](https://github.com/naota/embulk-filter-unique)

avoid import issues when Canvas Data has duplicate rows
  

### Additional Features
TimeZone correction
Specify option in table config to convert all datetime values to a given TimeZone... localized data vs UTC, or converting each query.

SQL to SQL

Have a staging area and a production environment, why not...
- Materialize views in production from views in Staging with a Query.
- Limit your data in production by JOINing down to current term

[___embulk-input-jdbc___](https://github.com/embulk/embulk-input-jdbc)

```yaml
  query: |
    SELECT submission_dim.*
    FROM submission_dim 
      JOIN assignment_dim ON (assignment_dim.id = submission_dim.assignment_id)
      JOIN course_dim ON (course_dim.id = assignment_dim.course_id)
      JOIN enrollment_term_dim ON (enrollment_term_dim.id = course_dim.enrollment_term_id)
    WHERE enrollment_term_dim.canvas_id IN (5518,5517,5516,5515,5514,5513)
    ORDER BY submission_dim.id ASC
```

```yaml
  query: |
    SELECT * FROM dbo.enrollment_vw WHERE canvas_term_id >= 5513
```

### Configs

Since Embulk uses plugins and each input and output source can be different, it makes sense that each Canvas Data table will have it's own config file. This might seem odd at first, not being a single application that imports all your data for you into your database.

However, let's consider some of the biggest pain points in managing Canvas Data as it is currently provided.
Developers like to generate DDLs of the schema, either manually with the documentation or dynamically using [schema.json](https://portal.inshosteddata.com/api/schema/latest)
	This is problematic, because the data defined in the docs or JSON are not complete.
    - Enumerables are missing
   	- Field Lengths are not always accurate
This means, that any time Canvas updates Canvas Data with new tables, new columns, deprecated columns one might have to manually ALTER TABLE, update their JSON to DDL generator, and evaluate any new 'overrides' necessary to correct the process...

[Canvas Data SDK overrides](https://github.com/Harvard-University-iCommons/canvas-data-sdk/blob/master/canvas_data/ddl_utils.py#L49)

[Canvancement overrides](https://github.com/jamesjonesmath/canvancement/blob/master/canvas-data/php/overrides.json)


IMHO, this is tech debt insanity.

These config files, which I will attempt to keep up-to-date, tagged with each schema version can be downloaded by you, and you can mix and massage your own workflow into these configs. You can easily read the upcoming [Canvas Data Release Notes](https://community.canvaslms.com/community/answers/releases/release-notes-canvas-data) and easily be prepared for the rollover without  a whole mess of alter tables and overrides to consider.

Embulk will recreate the whole table each time the config is run. Multiple import modes are available, including Insert, Insert Direct, Replace, Merge, Truncate and Truncate Insert. I use replace for every table except Requests where I use Insert.

You can run queries `before_load` and `after_load`.

After Load is where I have provided examples of Enumerables and Indexes.

### Provided Configs for Canvas Data
- MySQL
- SQL Server
- PostgreSQL
- _need help with Oracle and RedShift configs_

### Other potential advantages
Separating the Requests table out to sub tables based on need?


## Contributing
If you use this repository, please consider submitting Pull Requests to keep this resource up to date.
- If you expereince issues with the CSV parsing parameters
- If you experience issues with getting data and rows into SQL with errors, evaluate the issue and update the config
- If you can improve the SQL, please share.
- If you find you have ENUMERABLES not listed, please update the table config.
- If you find column lengths not accurate please update with the MAX number you've seen.
