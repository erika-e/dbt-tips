# dbt-tips

Collection of dbt Tips and Tricks

## Skip to a Section

* [Toolbox](#Toolbox) - basic tools that make working with dbt way easier
* [New to dbt](#New-to-the-dbt-Ecosystem?-Start-Here-with-Beginner-Tutorials) - start here for beginner tutorials
* [dbt CLI](#dbt-CLI) - tips and tricks for the dbt CLI
* [General Command Line](#Command-Line) - useful tricks for model and yml file manipulation from the command line
* [Git Tips](#Git-Tips) - useful in large dbt projects
* [Jinja](#Jinja) - it's surprisingly hard to learn basic Jinja concepts
* [dbt Macros and dbt Specific Jinja](#dbt-Macros-and-dbt-Specific-Jinja) - reference for dbt Macros and Jinja functions that come with dbt

## dbt Concepts and Practices

* [How dbtLabs stuctures dbt projects](https://discourse.getdbt.com/t/how-we-structure-our-dbt-projects/355)
* [dbtLabs dbt style guide](https://github.com/fishtown-analytics/corp/blob/master/dbt_style_guide.md)
* [HowTheydbt](https://github.com/stumelius/howtheydbt) - repo of company specific dbt practice publications
* [Overview of tests in dbt (updated for 0.20)](https://datacoves.com/post/an-overview-of-testing-options-in-dbt-data-build-tool)
* [Is Kimball Modeling Still Relevant?](https://discourse.getdbt.com/t/is-kimball-dimensional-modeling-still-relevant-in-a-modern-data-warehouse/225) and [Coalesce 2020: Kimball in the Context of the Modern Data Warehouse](https://www.youtube.com/watch?v=3OcS2TMXELU&list=PL0QYlrC86xQmPf9QUceFdOarYcv3ETSsz&index=19)
* [Make changes to your dbt source on your global project](https://discourse.getdbt.com/t/did-you-know-dbt-ships-with-its-own-project/764)
* [Overriding default schema and table names, using model file names as sources for schema and table names](https://discourse.getdbt.com/t/extracting-schema-and-model-names-from-the-filename/575)
* [Creating date dimensions tables with dbt](https://discourse.getdbt.com/t/date-dimensions/735/4)
* [Examples of custom schema tests](https://discourse.getdbt.com/t/examples-of-custom-schema-tests/181/5)
* [Sessionization best practices for large event tables](https://app.slack.com/client/T0VLPD22H/C0VLZPLAE/thread/C0VLZPLAE-1620406666.325100)
* [Updating a global dictionary value with Jinja](https://getdbt.slack.com/archives/CJN7XRF1B/p1608230420178900?thread_ts=1608156760.169900&cid=CJN7XRF1B)
* [None and undefined in logical tests in Jinja](https://getdbt.slack.com/archives/C2JRRQDTL/p1622223139233800?thread_ts=1622222319.232500&cid=C2JRRQDTL)
* [Analyzing dbt project performance with artifacts](https://discourse.getdbt.com/t/analyzing-fishtowns-dbt-project-performance-with-artifacts/2214)
* [dbt course refactoring legacy SQL to dbt](https://blog.getdbt.com/sql-refactoring-course/)
* [dbt analytics engineering guide](https://www.getdbt.com/analytics-engineering/)

### New to the dbt Ecosystem? Start Here with Beginner Tutorials

* dbt - start with the [Funadmentals Course](https://courses.getdbt.com/courses/fundamentals)
* SQL - check out the [Mode SQL tutorial](https://mode.com/sql-tutorial/)
* git - try [Think Like Git](http://think-like-a-git.net/) if you prefer to learn by reading, or [Learn Git Branching](https://learngitbranching.js.org/?locale=en_US) if you prefer something visual and interactive
* Jinja - check out [these tutorials by Prezemek Rogala](https://ttl255.com/jinja2-tutorial-part-1-introduction-and-variable-substitution/) who wrote the live parser I recommend

## Toolbox

* [dbt-utils](https://github.com/fishtown-analytics/dbt-utils) : Many multipurpose macros. If you're writing something complex or custom, there's probably a better way using functionality from dbt-utils
* [dbt-completion.bash](https://github.com/fishtown-analytics/dbt-completion.bash) : autocompletion for the dbt CLI [h/t](https://twitter.com/grantwinship/status/1397746292953128970)
* [dbt-codegen](https://github.com/fishtown-analytics/dbt-codegen) : macros that generate dbt code to the command line [h/t](https://twitter.com/JayPeeDevlin/status/1397743525270261766)
* [dbt-audit-helper](https://github.com/fishtown-analytics/dbt-audit-helper) : Zen and the art of data auditing. This package will change your life.
* [pre-commit-dbt](https://github.com/offbi/pre-commit-dbt) : Package of dbt pre-commit hooks that allow you to check quality of dbt project documentation, tests, etc
* [live Jinja Parser](https://j2live.ttl255.com/) : Useful tool for writing complex Jinja, does not include dbt-specific Jinja functions
* [yaml Checker](https://yamlchecker.com/) : yaml syntax validator

## dbt CLI

### Run And Test Modified and Downstream Models

`dbt run -m state:modified+ && dbt test -m state:modified+` [h/t](https://twitter.com/itswagg/status/1397758065043202049) and [dbt's caveats](https://docs.getdbt.com/reference/node-selection/state-comparison-caveats)

To list modifications since your last local run use `dbt ls -m state:modified --state ./target/`.

### Run Downstream Children from a Specific Node

`dbt run -m model_name_here+1` only run the children one layer downstream in the DAG
or
`dbt run -m model_name_here+n` where n is the offset

### Use Dictionaries as Selectors

`dbt run -m staging.airtable.*` to run all models in models/staging/airtable [h/t](https://twitter.com/grantwinship/status/1397732784844779529) or `dbt run -m staging/airtable`

### List All Models Downstream

`dbt ls -m mymodel+`

### Run Models with a Specific Tag and Their Parents

`dbt run -m +tag:foo` [h/t](https://getdbt.slack.com/archives/C2JRRQDTL/p1626273465175400)

### Run a Specific Snapshot

`dbt snapshot --select order_snapshot`

### Run Multiple Snapshots

`dbt snapshot --select model1 model2 ... modeln`

### Output dbt Logs to stdout Instead of the dbt.log File

`dbt --debug run` gives something similar to the logs [h/t](https://getdbt.slack.com/archives/C2JRRQDTL/p1622833644408600?thread_ts=1622833132.408500&cid=C2JRRQDTL)

### Run Tests on Sources Only

`dbt test --models source:*` [dbt docs](https://docs.getdbt.com/docs/building-a-dbt-project/tests)

### Run Tests By Materialization

`dbt test -m config.materialized:snapshot` or `dbt test -m config.materialzied:seed` not guarenteed to be supported [h/t](https://getdbt.slack.com/archives/C01UM2N7814/p1624369845111600?thread_ts=1624192939.105900&cid=C01UM2N7814) and caveat

### Run Tests by Tag After Tagging Models With a Specific Materialization

```yaml
# dbt_project.yml
...
snapshots:
  +tags: ['snapshot']
```

To run use `dbt test -m tag:snapshot` [h/t](https://getdbt.slack.com/archives/C01UM2N7814/p1624192939105900)

### Run A Macro With Arguements

`dbt run-operation my_macro --args '{"myarg1":"arrrgh", "myarg2":"aaaaaaargh"}'`

### dbt Keyboard Shortcuts

* `Command` + `/` to block comment / uncomment yml sections
* `Command` + `/` to block comment SQL sections in dbt cloud
* `Command` + `ENTER` to preview data in dbt cloud
* Use `F1` in dbt cloud to bring up a list of dbt cloud keyboard shortcuts [h/t](https://getdbt.slack.com/archives/CMZ2V0X8V/p1616095693024200?thread_ts=1616092005.022100&cid=CMZ2V0X8V)

## dbt Materializations

### Incremental

* [Incremental model strategy performance comparisons for BigQuery](https://discourse.getdbt.com/t/benchmarking-incremental-strategies-on-bigquery/981)
* [Incrementally --full-refresh an incremental model](https://getdbt.slack.com/archives/C0VLZPLAE/p1625066877102900)
* [Adding a column to an incremental model](https://discourse.getdbt.com/t/adding-a-column-to-an-incremental-model/55)
* [Performance improving ideas for lagre incremental models](https://discourse.getdbt.com/t/large-incremental-models/2096)

### Snapshots

* [dbt guide to snapshots](https://blog.getdbt.com/track-data-changes-with-dbt-snapshots/)
* [Handling hard deletes in snapshots](https://discourse.getdbt.com/t/handling-hard-deletes-from-source-tables-in-snapshots/1005/2)
* [Handling late arriving records in snapshots](https://discourse.getdbt.com/t/handling-late-arriving-records-in-fivetran-synced-snapshots/1641)
* [Adding a column to a snapshot with `check_cols` strategy](https://discourse.getdbt.com/t/migrating-a-snapshot-after-adding-a-check-col/485)
* [Use dynamic schemas for snapshots](https://discourse.getdbt.com/t/using-dynamic-schemas-for-snapshots/1070)

## VS Code

This section is stubby for now -- but I'd welcome contributions for how to make VS Code a more effective editor for dbt work!

* [Guide to using dbt in VSCode with lots of great setup tips](https://dbt-msft.github.io/dbt-msft-docs/docs/guides/vscode_setup/)
* [vscode-dbt-power-user](https://marketplace.visualstudio.com/items?itemName=innoverio.vscode-dbt-power-user)
* [brew installed dbt `dbt is not installed` message in VS Code](https://getdbt.slack.com/archives/C2JRRQDTL/p1635524229176400)

## Command Line

### Create Multiple (Blank) Model Files Following A Pattern

`touch {prefix1,prefix2}_model.sql`

Sometimes I'll use a spreadsheet to easily generate the list of prefixes, check out the `TEXTJOIN()` function in google sheets.

### Find and Delete Files Matching a Pattern Including in Subdirectories

Find first using `find path/to/directories/with*ifneeded -type f -name "example_file_prefix*"` to test your search criteria.

When happy, `find path/to/directories/with*ifneeded -type f -name "example_file_prefix*" -delete` to delete. [source](https://askubuntu.com/questions/377438/how-can-i-recursively-delete-all-files-of-a-specific-extension-in-the-current-di)

### Find Specific Lines in a .yml File and Delete Them

First, test with `sed -e '/regex_goes_here/{action goes here}' path_to_file(s)_to_act_on`

An example action to delete the matching line and subsequent lines could look like `{N;N;d}` to delete the line and the line after it. [More syntax examples for this use case](https://stackoverflow.com/questions/4396974/sed-or-awk-delete-n-lines-following-a-pattern)

When you're happy with your output, add `-i ''` to the start of your command. This will replace inplace without creating a new file. This `sed -i '' -e '/  - name: my_model_prefix[a-z]*/{N;N;N;d;}' models/staging/*.yml` will delete the matching line and the 3 lines that follow it in the matching file(s) and line(s).

### Shell Config for dbtmrt

`function dbtmrt () { dbt run -m $1 && dbt test -m $1}` for config
`dbtmrt model_name` to use [h/t](https://twitter.com/grantwinship/status/1397731624574492676)

### Two-Terminal Docs

1. run dbt docs generate
2. open a new terminal window to run dbt docs serve
3. just always have the docs open in the background - re run dbt docs generate in first terminal and refresh if needed
[h/t](https://twitter.com/clairebcarroll/status/1397734400134164480)

### Shell Script Examples for zsh

```zsh
# Functions
function dbtr() {
   dbt run -m $1 --fail-fast
   say done
}

function dbtrt() {
    dbt run -m $1 && dbt test -m $1
    say done
}

```

### Shell Script to Compile Audit Helper

Note that the below has only been tested on a mac, and requires you to create a SQL file called `audit_helper_template` in the `analysis` folder of your dbt project.

```zsh

function dbtah() {
    gsed -i "s/my_model/$1/" analysis/audit_helper_template.sql
    dbt compile -m audit_helper_template
    cat target/compiled/aula_data_models/analysis/audit_helper_template.sql | awk NF | pbcopy
    gsed -i "s/$1/my_model/" analysis/audit_helper_template.sql
    say copy pasta
}

```

```SQL

{%- set audit_model = "my_model" -%}
{%- set prod_schema = "production" -%}
{%- set dbt_relation = ref(audit_model) -%}

{%- set old_etl_relation=adapter.get_relation(
      database="ANALYTICS",
      schema=prod_schema,
      identifier=my_model
) -%}

{# Generate the audit query - update primary key as needed #}
{{ audit_helper.compare_relations(
    a_relation=old_etl_relation,
    b_relation=dbt_relation,
    primary_key="primary_key_update_before_running"
) }}
```

To run use `dbtah my_target_model`. Template heavily inspired by a tool created by [Lewis Davies](https://github.com/LewisDavies)

## Git Tips

### git mv to reorganize your project without losing history

If you need to move files from one directory to another, cd into the parent of the directories where the files are located, then run `git mv child_dir1/model_name child_dir2/with_nested_dirs_if_needed`. To make sure it worked, run `git status` afterwards and you should see your pending changes with `renamed:` ahead of the file name. Commit as usual, keep rocking with your history!

You can find lots of arguments about whether or not this *really* keeps the history on stack overflow and elsewhere around the internet. I've found that using this method preserves commit history, but ymmv.

### Rename a file without losing history

`git mv old_file_name.sql new_file_name.sql` for example. Same comment about but-does-it-really from above applies.

### Clean up branches and references to remote branches that have been merged

Remove local branches with `git branch -d my-merged-branch-name` for merged branches or `git branch -D unmerged-local-branch` for unmerged branches. You can run `git remote show origin` which will show all branches locally and on the origin. Running `git fetch --prune` will remove all branches in this listing that look like `refs/remotes/origin/branch-name` which are local snapshots of remote branches. [source](https://stackoverflow.com/questions/20106712/what-are-the-differences-between-git-remote-prune-git-prune-git-fetch-prune/20107184#20107184)

### Update your feature branch with new changes from master

You've been working along, and now git says your branch is out of date with the main branch. To fix this, swap over to the main branch with `git checkout main` and then get any new changes with `git pull --rebase`. Cool, now you're good locally, so switch over to your branch again with `git checkout feature` and use `git merge main` to update your branch.

### I made a bunch of changes on a branch and now my changes are failing

Assuming your branch has not been pushed up to the remote repo, you can get around this! First, create a temporary branch to save your work `git checkout -b temp_save` and then switch back to your develpoment branch `git checkout my-feat`. Next, reset your branch and your working tree to the last commit you are confident in with `git reset --hard 44e447a1`. What this will do is roll your branch and your working tree all the way back to the commit `44e447a1`. Next, you can apply the commits from your saved branch to the reset branch to 'walk forward' in time one by one until you find the problem that was introduced. To apply a commit from one branch to another, use `git cherry-pick 1b132878`. This will create a totally new commit on your branch with the changes from the commit `1b132878`. It's not a good idea to do this if you've already pushed your branch, since you're then rewriting history and potentially others' commits.

## Jinja

A live parser is an incredibly useful tool while you're learning and writing jinja. I like [this one](https://j2live.ttl255.com/)

### Jinja Delimiters

* A statement looks like `{% ... %}`
* An expression looks like `{{ ... }}`
* A comment looks like `{# ... #}` [h/t](http://courses.getdbt.com)
* To escape a sequence you can use `{{ '{{ my_escaped_var}}'~`
* To escape longer blocks of code you can use `{% raw %}  {% endraw %}` [Jinja2 docs](https://jinja.palletsprojects.com/en/3.0.x/templates/?highlight=endraw#escaping)

### Comments that Don't Show in Compiled SQL

`{# You won't see me #}` vs `--I will show up all day` vs `{# /* I won't show up AND your linter won't freak out /* #}` [dbt discourse](https://discourse.getdbt.com/t/removing-jinja-comments-from-compiled-sql/80)

### Declaring Variables

`{% set my_list = ['one','two',three'] %}` and accessing items from lists `{{ my_list[1] }}` will return `two`

```Jinja2
{% set my_dict = {
    'key1': 'value1',
    'key2': 'value2'
} %}
```

A general best practice is to do this at the top of your file.

### If Else Blocks

```Jinja2
{% set condition = True %}

{% if condition %}
what to execute when true

{% else %}
what to execute otherwise

{% endif %}
```

### For Loops

```Jinja2
{% set my_list = ['one','two',three'] %}

{% for item in my_list %}

{{item}}

{% endfor %}
```

## dbt Macros and dbt Specific Jinja

### When to Use if excute in Macros

[When you have queries that need to execute during the parse phase!](https://docs.getdbt.com/reference/dbt-jinja-functions/execute)

### Macros Calling Other Macros

[Example of a Macro Calling Another Macro](https://github.com/fishtown-analytics/dbt/issues/469)

### For Loops for Select Statements

For loops for select statements need special handling to prevent a comma after the last item in the select. The hyphens are used to trim the whitespace for better looking compiled SQL.

```SQL
SELECT
{% for item in list_of_items -%}
{{item}}
{%- if not loop.last -%}
,
{%- endif %}
{% endfor -%}
```

### Macros with Parameters

```Jinja2
{% macro my_macro(required_parameter, optional_parameter = default_value) %}
...
{% endmacro %}
```

### adapter - a dbt Jinja Function

[adapter](https://docs.getdbt.com/reference/dbt-jinja-functions/adapter) performs many useful functions and adapts them to the specific database context you're using

* [dispatch](https://docs.getdbt.com/reference/dbt-jinja-functions/adapter#dispatch) - database specific versions of macros
* [get_missing_columns](https://docs.getdbt.com/reference/dbt-jinja-functions/adapter#get_missing_columns) - find columns existing in one relation but missing in another, return as a list - can identify new coulmns in sources
* [expand_target_column_types](https://docs.getdbt.com/reference/dbt-jinja-functions/adapter#expand_target_column_types) - to make one relation match another, some limitations
* [get_relation](https://docs.getdbt.com/reference/dbt-jinja-functions/adapter#get_relation) - returns a relation object from the database from the provided database, schema, identifier
* [get_columns_in_relation](https://docs.getdbt.com/reference/dbt-jinja-functions/adapter#get_columns_in_relation) - returns a list of columns in a relation
* [create / drop schema or relation](https://docs.getdbt.com/reference/dbt-jinja-functions/adapter#create_schema) - methods for creating, dropping, or renaming schemas or relations in the database
