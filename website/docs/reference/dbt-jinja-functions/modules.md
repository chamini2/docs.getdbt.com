---
title: "modules"
id: "modules"
---

The `modules` variable in the Jinja context contains useful Python modules for operating on data.

## datetime
This variable is a pointer to the Python [datetime](https://docs.python.org/3/library/datetime.html) module, which supports complex date and time logic.

It includes the modules contexts of `date`, `datetime`, `time`, `timedelta`, and `tzinfo`.

**Usage**

```
{% set now = modules.datetime.datetime.now() %}
{% set three_days_ago_iso = (now - modules.datetime.timedelta(3)).isoformat() %}
```
This module will return the current date and time on every Jinja evaluation. 
For the date and time of the start of the run, please see
[run_started_at](run_started_at).

## pytz
This variable is a pointer to the Python [pytz](https://pypi.org/project/pytz/) module, which supports timezone logic.

**Usage**

```
{% set dt = modules.datetime.datetime(2002, 10, 27, 6, 0, 0) %}
{% set dt_local = modules.pytz.timezone('US/Eastern').localize(dt) %}
{{ dt_local }}
```

## re
This variable is a pointer to the Python [re](https://docs.python.org/3/library/re.html) module, which supports regular expressions.

**Usage**

```
{% set my_string = 's3://example/path' %}
{% set s3_path_pattern = 's3://[a-z0-9-_/]+' %}

{% set re = modules.re %}
{% set is_match = re.match(s3_path_pattern, my_string, re.IGNORECASE) %}
{% if not is_match %}
    {%- do exceptions.raise_compiler_error(
        my_string ~ ' is not a valid s3 path'
    ) -%}
{% endif %}
```
