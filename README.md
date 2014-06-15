HNMP is a high-level Python library to ease the pain of retrieving and processing data from SNMP-capable devices such as network switches, routers, and printers. It's not meant to provide everything SNMP has to offer, but to get rid of most of the suck inherent to writing Munin or Icinga plugins that process SNMP data.

HNMP is meant to be used like this:

1. acquire MIB files (optional, some luck required)
2. use a MIB browser application like [this freeish and cross-platform one](http://ireasoning.com/mibbrowser.shtml) to figure out the numeric OIDs you're interested in
3. use HNMP to retrieve your data and do some light processing
4. put man on moon :rocket:

Usage
-----

```python
>>> from hnmp import SNMP
>>>
>>> snmp = SNMP("example.com")

# get a single value
>>> uptime = snmp.get("1.3.6.1.2.1.1.3.0")
>>> uptime
datetime.timedelta(412, 29152)

# build a table
>>> wifi_clients = snmp.table(
>>>     "1.3.6.1.4.1.14179.2.1.4.1",
>>>     columns={
>>>         3: "username",
>>>         25: "protocol",
>>>     },
>>>     column_value_mapping={
>>>         "protocol": {
>>>             3: "802.11g",
>>>             6: "802.11n",
>>>         },
>>>     },
>>> )
>>>
>>> table.columns["username"]
("jdoe", "rms", "bwayne")
>>> table.columns["protocol"]
("802.11g", "802.11n", "802.11n")
>>> table.rows[0]["username"]
"jdoe"

# conveniently count column values
>>> table.columns["protocol"]
("802.11g", "802.11n", "802.11n")
>>> table.columns["protocol"].value_count
{"802.11g": 1, "802.11n": 2}

# helpers for converting MAC and IP addresses
>>> from hnmp import ipv4_address, mac_address
>>> raw_string = snmp.get("1.3.6.1.4.1.9.9.513.1.1.1.1.2.[...]")
>>> raw_string
't&\xac\x1b\xe7\xa1'
>>> mac_address(raw_string)
'74:26:ac:1b:e7:a1'
>>> ipv4_address(snmp.get("1.3.6.1.4.1.9.9.513.1.1.1.1.11.[...]"))
'10.1.2.3'
```

Install
-------

```
pip install hnmp
```

FAQ
---

### Why doesn't HNMP support loading MIB files?

Depending on MIB files would make the calling piece of code harder to distribute (since you need to include the MIBs, which may have some nasty non-free license attached to them). I consider MIB files a means to manually discover OIDs, nothing more. HNMP is biased towards use in scripts rather than full-blown applications. Having to use a library is bad enough for scripts, MIBs would just make your script even more unwieldy.

### Why doesn't HNMP support SNMPv3?

The need just hasn't arisen yet. Version 2c works pretty well for my purposes.

------------------------------------------------------------------------

![PyPI downloads](http://img.shields.io/pypi/dm/hnmp.svg) &nbsp; ![PyPI version](http://img.shields.io/pypi/v/hnmp.svg) &nbsp; ![Python 2.7](http://img.shields.io/badge/Python-2.7-green.svg) &nbsp; ![Python 3.x](http://img.shields.io/badge/Python-3.x-green.svg) &nbsp; ![PyPI license](http://img.shields.io/badge/License-ISC-red.svg)
