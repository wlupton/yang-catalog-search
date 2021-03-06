# YANG Search Data Maintenance

All those scripts maintain the MySQL tables `modules` and `yindex`.

A cronjob is executed every minute and calls: `process-changed-mods.py --time -1`

## process-changed-mods.py

Take on argument: the number of minutes to search for new modules.

Read the JSON file YANG_CACHE_FILE for the list of modules (also making a .bak before truncating it to 0), it is actually the list of modules to be processed.

Read the JSON file YANG_DELETE_FILE for the list of deleted modules (also making a .bak before truncating it to 0), then request SQL to delete the rows related to deleted modules.

**Note:** the two JSON files are actually created by an external process calling the web service at /yang-search/metadata_update/

Finally, calls `build_yindex.sh` with the `--time` argument and the list of all modules to be processed.


## build_yindex.sh

Build the list of all modules modified since the --time (else for all modules), and for all modules to be processed:
* Using the Yang Catalog pyang plugin, it generates the SQL statements to insert the information in the `yindex` and `modules` tables;
* Using the ` -f json-tree` pyang plugin, it generates the tree .json;
* Using the ` -f cxml` pyang plugin, it saves the information for Yang Explorer[https://github.com/CiscoDevNet/yang-explorer].

Then, it calls `process-catalog-file.py` for all catalogs (from environment variable YANG_CATALOG_FILES set in yindex.env).

Finally, it calls `add-catalog-data.py`.

**NOTE**: this script has very poor performance as it forks several times per module for pyang analysis, for MySQL statements, ... to be rewritten completely in Python and calling natively the pyang & MySQL bindings.

## process-catalog-file.py

## add-catalog-data.py

