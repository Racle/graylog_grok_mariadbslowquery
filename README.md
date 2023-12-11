[![license](https://img.shields.io/github/license/mashape/apistatus.svg?maxAge=2592000)](https://opensource.org/licenses/MIT)

# MariaDB Slow Query Log GROK pattern for Graylog

Forked from [zionio/graylog_grok_mysqlslowquery](https://github.com/zionio/graylog_grok_mysqlslowquery) and modified to work with MariaDB slowlog.

## Pattern

    name: "MARIADBSLOWQUERYLOG"
    pattern: "(?s) Time: %{DATA:mysql_slow_querydate}\n%{GREEDYDATA}User@Host: (?:%{USERNAME:mysql_slow_clientuser})\[(?:%{DATA:mysql_slow_clientdbname})\] @ (?:%{DATA:mysql_slow_clienthost}) \[(?:%{DATA:mysql_slow_clientip})\]\n# Thread_id:%{DATA} Schema: %{GREEDYDATA:mysql_slow_schema}%{SPACE}QC_hit: %{DATA:mysql_slow_qc_hit}# Query_time: %{NUMBER:mysql_slow_querytime:float}(?:%{SPACE})Lock_time: %{NUMBER:mysql_slow_locktime:float}(?:%{SPACE})Rows_sent: %{NUMBER:mysql_slow_rowssent:int}(?:%{SPACE})Rows_examined: %{NUMBER:mysql_slow_rows_examined:int}%{GREEDYDATA}# Rows_affected: %{NUMBER:mysql_slow_rows_affected:int}%{SPACE}Bytes_sent: %{NUMBER:mysql_slow_bytes_sent:int}(?:%{GREEDYDATA})SET timestamp=%{NUMBER:mysql_slow_timestamp};\n%{GREEDYDATA:mysql_slow_query};"

## Example message

    # Time: 231211 10:39:41
    # User@Host: root[root] @  [1.1.1.1]
    # Thread_id: 1  Schema: db  QC_hit: No
    # Query_time: 9.000000  Lock_time: 0.000011  Rows_sent: 1  Rows_examined: 10
    # Rows_affected: 0  Bytes_sent: 0
    use db;
    SET timestamp=1672531200;
    SELECT * FROM `table`;

## Fields

    mysql_slow_querydate 231211 10:39:41
    mysql_slow_clientuser root
    mysql_slow_clientdbname root
    mysql_slow_clienthost
    mysql_slow_clientip 1.1.1.1
    mysql_slow_schema db
    mysql_slow_qc_hit No
    mysql_slow_querytime 9.000000
    mysql_slow_locktime 0.000011
    mysql_slow_rowssent 1
    mysql_slow_rows_examined 10
    mysql_slow_rows_affected 0
    mysql_slow_bytes_sent 0
    mysql_slow_timestamp 1672531200
    mysql_slow_query SELECT * FROM `table`;

## Input (Filebeat)

Collector Configuration

    filebeat.inputs:
    - input_type: log
      paths:
        - /path/to/mariadb/slowlog.log
      type: log
      multiline.pattern: '^\#[[:space:]]Time'
      multiline.negate: true
      multiline.match: after

## Installation

Go to **Graylog Web Interface** -> **System** -> **Content Packs** then select _content_pack.json_ file and upload it and then Install.

[![screen1](https://i.imgbox.com/HAsDC4FR.png)](https://i.imgbox.com/wP2n4HXH.png)

## Extractor

- Go to **Graylog Web Interface** -> **System** -> **Inputs** then select filebeat stream and click **Manage extractors**.
- Click **Load Message** and then in message select **Select extractor type** -> **Grok pattern extractor**.
- Check **Named Captures Only**.
- In **Grok pattern** add `%{MARIADBSLOWQUERYLOG}`.

## Check

Created and tested on
`Graylog Graylog 5.1.5`
