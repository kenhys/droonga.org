---
title: Catalog
layout: en
---

* TOC
{:toc}

## Abstract {#abstract}

## Usage {#usage}

This [`version`](#parameter-version) of `catalog` will be available from Droonga 1.0.0.

## Syntax {#syntax}

    {
      "version": <Version number>,
      "effective_date": "<Effective date>",
      "datasets": {
        "<Name of the dataset 1>": {
          "n_workers": <Number of workers>,
          "plugins": [
            "Name of the plugin 1",
            ...
          ],
          "schema": {
            "<Name of the table 1>": {
              "flags"             : "<Flags for the table>",
              "key_type"          : "<Type of the primary key>",
              "default_tokenizer" : "<Default tokenizer>",
              "normalizer"        : "<Normalizer>",
              "columns" : {
                "<Name of the column 1>": {
                  "flags"  : "<Flags for the column>",
                  "type"   : "<Type of the value>",
                  "source" : "<Name of a column to be indexed>"
                },
                "<Name of the column 2>": { ... },
                ...
              }
            },
            "<Name of the table 2>": { ... },
            ...
          },
          "fact": "<Name of the fact table>",
          "replicas": [
            {
              "dimension": "<Name of the dimension column>",
              "slicer": "<Name of the slicer function>",
              "slices": [
                {
                  "label": "<Label of the slice>",
                  "partition": {
                    "address": "<Address string of the partition>"
                  }
                },
                ...
              }
            },
            ...
          ]
        },
        "<Name of the dataset 2>": { ... },
        ...
      }
    }

## Details {#details}

### Catalog definition {#catalog}

Value
: An object with the following key/value pairs.

#### `version` {#parameter-version}

Abstract
: Version number of the catalog file.

Value
: `2`. (Specification written in this page is valid only when this value is `2`)

Default value
: None. This is a required parameter.

Inheritable
: False.

#### `effective_date` {#paramter-effective_date}

Abstract
: The time when this catalog becomes effective.

Value
: A local time string formatted in the [W3C-DTF](http://www.w3.org/TR/NOTE-datetime "Date and Time Formats"), with the time zone.

Default value
: None. This is a required parameter.

Inheritable
: False.

#### `datasets` {#parameter-datasets}

Abstract
: Definition of datasets.

Value
: An object keyed by the name of datasets, the values are [`dataset` definitions](#dataset).

Default value
: None. This is a required parameter.

Inheritable
: False.

#### `n_workers` {#parameter-n_workers}

Abstract
: The number of worker processes spawned for each database instance.

Value
: An integer value.

Default value
: 0 (No worker. All operations are done in the master process)

Inheritable
: True. Available in `dataset` and `partition` definition.

### Dataset definition {#dataset}

Value
: An object with the following key/value pairs.

#### `plugins` {#parameter-plugins}

Abstract
: plugin names.

Value
: An array of strings.

Default value
: None. This is a required parameter.

Inheritable
: True. Available in `dataset` and `partition` definition.

#### `schema` {#parameter-schema}

Abstract
: Definition of tables and their columns.

Value
: An object that each key gives the name of the table and the value is an object with [`table` definitions](#table).

Default value
: None. This is a required parameter.

Inheritable
: True. Available in `dataset` and `partition` definition.

#### `fact` {#parameter-fact}

Abstract
: Name of the fact table.

Value
: Any of table names appears in [`schema` parameter](#parameter-schema).

Default value
: None. This is a required parameter.

Inheritable
: True. Available in `dataset` and `partition` definition.

#### `replicas` {#parameter-replicas}

Abstract
: Definition of replicas which store the contents of the dataset.

Value
: An array of [`partition` definitions](#partition).

Default value
: None. This is a required parameter.

Inheritable
: False.

### Table definition {#table}

Value
: An object with the following key/value pairs.

#### `flags` {#parameter-table-flags}

Abstract
: 

Value
: 

Default value
: 

Inheritable
: 

#### `key_type` {#parameter-key_type}

Abstract
: 

Value
: 

Default value
: 

Inheritable
: 

#### `default_tokenizer` {#parameter-default_tokenizer}

Abstract
: 

Value
: 

Default value
: 

Inheritable
: 

#### `normalizer` {#parameter-normalizer}

Abstract
: 

Value
: 

Default value
: 

Inheritable
: 

#### `columns` {#parameter-columns}

Abstract
: 

Value
: 

Default value
: 

Inheritable
: 


### Column definition {#column}

Value
: An object with the following key/value pairs.

#### `flags` {#parameter-column-flags}

Abstract
: 

Value
: 

Default value
: 

Inheritable
: 

#### `type` {#parameter-type}

Abstract
: 

Value
: 

Default value
: 

Inheritable
: 

#### `source` {#parameter-source}

Abstract
: 

Value
: 

Default value
: 

Inheritable
: 

### Partition definition {#partition}

Value
: An object with the following key/value pairs.

#### `dimension` {#parameter-dimension}

Abstract
: One of the columns of the fact table to slice data.

Value
: Name of a scalar type column of the fact table.

Default value
: "_key"

Inheritable
: True. Available in `dataset` and `partition` definition.

#### `slicer` {#parameter-slicer}

Abstract
: Function to slice the value of dimension column.

Value
: Name of slicer function.

Default value
: "hash"

Inheritable
: True. Available in `dataset` and `partition` definition.

#### `slices` {#parameter-slices}

Abstract
: Definition of slices which store the contents of the data.

Value
: An object or An array. When the [`slicer` parameter](#parameter-slicer) is `hash`,

An array of [`partition` definitions](#partition).

Abstract
: 

Value
: 

Default value
: 

Inheritable
: 


### Slice definition {#slice}

Value
: An object with the following key/value pairs.

#### `weight` {#parameter-weight}

Abstract
: 

Value
: 

Default value
: 

Inheritable
: 

#### `partition` {#parameter-partition}

Abstract
: 

Value
: 

Default value
: 

Inheritable
: 