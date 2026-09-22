
## Experimental API methods

There are also some experimental API methods documented only by name:

* system_versions
* conf_profiles
* conf_languages
* lookup_address_records
* lookup_delegation_data
* job_create
* job_status
* job_results
* job_params
* batch_create
* batch_status
* batch_results
* batch_params
* batch_list
* batch_cancel
* batch_delete
* domain_history
* user_create           TO delete


job is the new test


### Data types

Here there is some change for datatype



#### API key

Basic data type: string

A string of alphanumerics, hyphens (`-`) and underscores (`_`), of at least 1
and at most 512 characters.
I.e. a string matching `/^[a-zA-Z0-9-_]{1,512}$/`.

Represents the password of an authenticated account (see *[Privilege levels]*)


#### Batch id

Basic data type: string

A string of exactly 16 lower-case hex-digits starting with `B` matching `/^B[0-9a-f]{16}$/`.

Each *batch* has a unique *batch_id*.


#### Job id

Basic data type: string

A string of exactly 16 lower-case hex-digits starting with `J` matching `/^J[0-9a-f]{16}$/`.

Each *job* has a unique *job_id*.


#### Job result

Basic data type: object

The object has four keys, `"module"`, `"message"`, `"level"` and `"testcase"`.

* `"module"`: a string. The *test module* that produced the result.
* `"message"`: a string. A human-readable *message* describing that particular result.
* `"level"`: a [*severity level*][Severity level]. The severity of the message.
* `"testcase"`: a string. The *[Test Case Identifier][Test Case Identifiers]* of the *[Test Case][Test Cases]* that produced the result.

Sometimes additional keys are present.

* `"ns"`: a [*domain name*][Domain name]. The name server used by the *test module*.
This key is added when the module name is `"NAMESERVER"`.



#### Status

State of a job or a batch, can be one of these five:
`waiting`, `running`, `completed`, `cancelled` or `crashed`.

### API methodes

#### `system_versions`

Replaces: version_info

**Request:**
```json
￼{
  "jsonrpc": "2.0",
  "method": "system_versions",
  "params": {},
  "id": 1
}
```

**Response:**
```json
{
  "jsonrpc": "2.0",
  ￼"result": {
    "zonemaster_backend": "2026.2.0",
    "zonemaster_engine": "2026.2.0",
    "zonemaster_ldns": "2026.2.0",
    "database_schema_version": "1.2.0",
    "api_version": "2.0.0"
  },
  "id": 1
}
```

**result**

An object with the following properties:

* "zonemaster_ldns": A string. The version number of the running Zonemaster LDNS.
* "zonemaster_backend": A string. The version number of the running Zonemaster Backend.
* "zonemaster_engine": A string. The version number of the Zonemaster Engine used by the RPC API daemon.
* "database_schema_version": A string. The version of the database used by Zonemaster Backend.
* "api_version": A string. The version of the current API.


#### `conf_profiles`

Replaces: `profiles_names`

**Request:**
```json
{
  "jsonrpc": "2.0",
  "method": "conf_profiles",
  "params": {},
  "id": 2
}
```

**Response:**
```json
{
  "jsonrpc": "2.0",
  "result": {
    "profiles": ["default", "test-profile-1"]
  },
  "id": 2
}
```

**result**

An array of [*Profile names*][Profile name] in lower case. `"default"` is always included.


#### `conf_languages`

Replaces: `get_language_tags`
Returns the set of valid [*language tags*][Language tag].


**Request:**
```json
{
  "jsonrpc": "2.0",
  "method": "conf_languages",
  "params": {},
  "id": 3
}
```

**Response:**
```json
{
  "jsonrpc": "2.0",
  "result": {
    "language_tags": ["en", "fr", "sv", "da", "nb", "fi", "es"]
  },
  "id": 3
}
```

**result**

An array of [*language tags*][Language tag]. It is never empty.


#### `conf_backend`

Exposes `age_reuse_previous_test` and other [backend config values](../../configuration/backend.md).  
Related: [#1255](https://github.com/zonemaster/zonemaster-backend/issues/1255)


**Request:**
```json
{
  "jsonrpc": "2.0",
  "method": "conf_backend",
  "params": {},
  "id": 4
}
```

**Response:**
```json
{
  "jsonrpc": "2.0",
  "result": {
    "age_reuse_previous_test": 300,
    "max_zonemaster_execution_time": 600,
    "batch_enabled": true,
    "max_batch_size": 1000,
    "anonymous_batch_enabled": true,
    "anonymous_batch_max_size": 100
  },
  "id": 4
}
```

**result**

An object with the following properties:
* "age_reuse_previous_test": An integer.
* "max_zonemaster_execution_time": An integer. Maximum time allocated to a job to finnish before being canceled, in seconds.
* "batch_enabled": A boolean. Set to true if the instance allow authenticated batch.
* "max_batch_size": An integer. Maximum number of domain by authenticated batch.
* "anonymous_batch_enabled": A boolean. Set to true if the instance allow anonymous job batch.
* "anonymous_batch_max_size": An integer. Maximum number of domain by anonymous batch.



#### `lookup_address_records`

Replaces: `get_host_by_name`

Looks up the A and AAAA records for a hostname ([*domain name*][Domain name]) on the public Internet.

**Request:**
```json
{
  "jsonrpc": "2.0",
  "method": "lookup_address_records",
  "params": {
    "hostname": "ns1.example.com"
  },
  "id": 5
}
```

**Response:**
```json
{
  "jsonrpc": "2.0",
  "result": {
    "addresses": { <- replace by hostname ?
      "IPv4" : ["192.0.2.1"],
      "IPv6" : ["2001:db8::1"]
    },
  "id": 5
}
```

**params**

An object with the property:

* `"hostname"`: A [*domain name*][Domain name], required. The hostname whose IP addresses are to be resolved.


**result**

An `addresses` object containing separate IPv4 and IPv6 arrays. Each array may contain multiple IP addresses and is empty when no address of that type is found.

> TODO: do we need to be able to ask muliple hostname at once ?
> in this case replace request hostname by an array and encapsulate addresses response inside an object with `hostname` as key.


#### `lookup_delegation_data`

Replaces: `get_data_from_parent_zone`

Returns all the NS/IP and DS/DNSKEY/ALGORITHM pairs of the domain from the
parent zone.


**Request:**
```json
{
  "jsonrpc": "2.0",
  "method": "lookup_delegation_data",
  "params": {
    "domain": "example.com"
  },
  "id": 6
}
```

**Response:**
```json
{
  "jsonrpc": "2.0",
  "result": {
    "nameservers": [
      { "hostname": "ns1.example.com", "ip": "192.0.2.1" },
      { "hostname": "ns1.example.com", "ip": "2001:67c:124c:100a::45"},
      { "hostname": "ns2.example.com", "ip": "192.0.2.2" },
      ...
    ],
    "ds_records": [
      { 
        "keytag": 54636, 
        "algorithm": 8, 
        "digtype": 2, 
        "digest": "cb496a0dcc2dff88c6445b9aafae2c6b46037d6d144e43def9e68ab429c01ac6"
      },
      {
        "keytag": 54636,
        "digest": "fd15b55e0d8ee2b5a8d510ab2b0a95e68a78bd4a",
        "algorithm": 5,
        "digtype": 1
      }
    ]
  },
  "id": 6
}
```
> TODO: i dont like the `nameservers` array returned. 


>
> Note: The above example response was abbreviated for brevity to only include
> the first two elements in each list. Omitted elements are denoted by a `...`
> symbol.
>


**params**

An object with the properties:

* `"domain"`: A [*domain name*][Domain name], required. The domain whose DNS records are requested.
* `"language"`: A [*language tag*][Language tag], optional, used for validation error messages
  translation, if not provided messages will be untranslated (in English).

**result**

An object with the following properties:

* `"nameservers"`: A list of [*name server*][Name server] objects representing the nameservers of the given [*domain name*][Domain name].
* `"ds_records"`: A list of [*DS info*][DS info] objects representing delegation signer (DS record data) of the given [*domain name*][Domain name].

> TODO: redo `nameservers` description

**error**

* If any parameter is invalid an error code of -32602 is returned. The `data` property contains an array of all errors, see [Validation error data].

  Example of error response:

```json
{
  "jsonrpc": "2.0",
  "id": 1624630143271,
  "error": {
    "data": [
      {
        "message": "The domain name character(s) are not supported",
        "path": "/domain"
      }
    ],
    "code": "-32602",
    "message": "Invalid method parameter(s)."
  }
}
```


#### `lookup_tld_url`

Returns a URL for the closest TLD to the domain name in the request, if available
and matching policy of backend and policy of the TLD. The response can also be
without URL of different reasons. For context and details see
[TLD URL Specification].

Example 1 request:
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "lookup_tld_url",
  "params": {
    "domain": "zonemaster.net"
  }
}
```
Example 1 response:
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "tld": "net",
    "url": "http://www.verisigninc.com",
    "source": "IANA RDAP"
  }
}
```

Example 2 request:
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "lookup_tld_url",
  "params": {
    "domain": "zonemaster.se"
  }
}
```
Example 2 response:
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "tld": "se",
    "url": "http://www.internetstiftelsen.se",
    "source": "TXT RECORD"
  }
}
```

Example 3 request:
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "lookup_tld_url",
  "params": {
    "domain": "zonemaster.fr"
  }
}
```
Example 3 response:
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "tld": "fr",
    "url": "http://www.afnic.fr",
    "source": "BACKEND CONF"
  }
}
```

Example 4 request:
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "lookup_tld_url",
  "params": {
    "domain": "zonemaster.xa"
  }
}
```

Example 4 response:
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "tld": "xa"
  }
}
```
(No URL found; blocked by either backend configuration or TLD policy in TXT record)

**params**

An object with the property:

* `"domain"`: A [*domain name*][Domain name], required. The domain name, for
  which a URL will be optionally determined.


**result**

An object with the following properties:

* `"tld"`: The identified TLD from the domain name in the query. Absent if the
  domain name is the root node ".". In [A-label][RFC 5890#2.3.2.1] shape if the
  TLD is an `IDN label`.
* `"url"`: An http or https URL. Present if and only if
  [`TLD URL SETTINGS.enable_tld_url`][TLD URL SETTINGS section.enable_tld_url]
  is true and a URL was determined (see
  ["Determination of URL"][TLD URL Specification#det-of-url]).
* `"source"`: A string from the following set. Present if and only if both
  "`url`" is present and
  [`TLD URL SETTINGS.include_source`][TLD URL SETTINGS section.include_source]
  is true.
  * `"BACKEND CONF"`: The URL is configured in the `backend_config.ini`
  configuration file.
  * `"TXT RECORD"`: The URL is fetched from the TLD TXT record.
  * `"IANA RDAP"`: The URL is fetched from the IANA RDAP database.

**error**

If the domain parameter is missing or there are validation errors, an error
code of -32602 is returned. The `data` property contains an array of all errors,
see [Validation error data].

Example 1 request with error:
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "lookup_tld_url",
  "params": { 
    "something": "nothing"
  }
}
```
Example 1 of response:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "error": {
    "data": [
      {
        "message": "The domain name is missing",
        "path": "/domain"
      }
    ],
    "code": -32602,
    "message": "Invalid method parameter(s)."
  }
}
```

Example 2 request with error:
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "lookup_tld_url",
  "params": {
    "domain": "-%zonemaster.se"
  }
}
```
Example 2 of response:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "error": {
    "data": [
      {
        "path": "/domain",
        "message": "The domain name character(s) are not supported"
      }
    ],
    "code": -32602,
    "message": "Invalid method parameter(s)."
  }
}
```


#### `job_create`

Enqueues a new job and returns the job id of the job.

client_id and client_version have been discarded ->https://github.com/zonemaster/zonemaster-backend/issues/1122

but maybe we have to add custom data store :
- https://github.com/zonemaster/zonemaster-backend/issues/1122
- https://github.com/zonemaster/zonemaster-backend/issues/1116

language removed , it s not used !
the language parameter on start_domain_test is persisted in the params JSON column but has no effect on test execution, deduplication, or result formatting.

> nameservers consitancy with lookup_delegation_data 

```json
{
  "jsonrpc": "2.0",
  "id": 42,
  "method": "job_create",
  "params": {
    "domain": "zonemaster.net",
    "profile": "default",
    "nameservers": [
      {
        "ip": "2001:67c:124c:2007::45",
        "ns": "ns3.nic.se"
      },
      {
        "ip": "192.93.0.4",
        "ns": "ns2.nic.fr"
      }
    ],
    "ds_info": [],
    "ipv6": true,
    "ipv4": true
  }
}
```

Example response:
```json
{
  "jsonrpc": "2.0",
  "id": 42,
  "result": {
    "job_id": "Jc45a3f8256c4a155"
  }
}
```


**params**

An object with the following properties:

**Required**

* `"domain"`: A [*domain name*][Domain name]. The zone to test.

**Optional**

* `"ipv6"`: A boolean. (default: [`net.ipv4`][net.ipv4] profile value). Used to enable or disable testing over IPv4 transport protocol.
* `"ipv4"`: A boolean. (default: [`net.ipv6`][net.ipv6] profile value). Used to enable or disable testing over IPv6 transport protocol.
* `"nameservers"`: A list of [*name server*][Name server] objects. (default: `[]`). Used to perform un-delegated test.
* `"ds_info"`: A list of [*DS info*][DS info] objects. (default: `[]`). Used to perform un-delegated test.
* `"profile"`: A [*profile name*][profile name]. (default: `"default"`). Run the tests using the given profile.
* `"priority"`: A [*priority*][Priority]. (default: `10`) ###TODO: explain###
* `"queue"`: A [*queue*][Queue]. (default: `0`) ###TODO: explain###


#### `job_status`


Reports on the progress of a *job*.

Example request:

*Valid syntax:*
```json
{
  "jsonrpc": "2.0",
  "id": 5,
  "method": "job_status",
  "params": {
    "job_id": "Jc45a3f8256c4a155"
  }
}
```

Example response:
```json
{
  "jsonrpc": "2.0",
  "id": 5,
  "result": {
    "state": "running",
    "progress": 32,
  }
}
```

**params**

* `job_id` :

**results**

* "state": 
* "progress": pourcentage of progression for the test.


#### `job_results`


Return all [*job result*][job result] objects of a *test*, with *messages* in the requested language as selected by the [*language tag*][Language tag].

Example request:
```json
{
  "jsonrpc": "2.0",
  "id": 6,
  "method": "job_results",
  "params": {
    "job_id": "Jc45a3f8256c4a155",
    "language": "en"
  }
}
```

The `job_id` parameter must match the `result` in the response to a [`job_create`][API job_create]
call, and that test must have been completed.

> `hash_id` replaced by `job_id`

Example response:
```json
{
  "jsonrpc": "2.0",
  "id": 6,
  "result": {
    "created_at": "2016-11-15T11:53:13Z",
    "started_at": "2016-11-15T12:01:52Z",
    "finished_at": "2016-11-15T13:12:02Z", 
    "job_id": "Jc45a3f8256c4a155",
    "params": {
      "ds_info": [],
      "domain": "zonemaster.net",
      "profile": "default",
      "ipv6": true,
      "nameservers": [
        {
          "ns": "ns3.nic.se",
          "ip": "2001:67c:124c:2007::45"
        },
        {
          "ip": "192.93.0.4",
          "ns": "ns2.nic.fr"
        }
      ],
      "ipv4": true,
    },
    "testcase_descriptions": {
      "ZONE08": "MX is not an alias",
      "SYNTAX05": "Misuse of '@' character in the SOA RNAME field",
      ...
    },
    "results": [
      {
        "module": "SYSTEM",
        "message": "Using version v1.0.14 of the Zonemaster engine.\n",
        "level": "INFO"
      },
      {
        "message": "Configuration was read from DEFAULT CONFIGURATION\n",
        "level": "INFO",
        "module": "SYSTEM"
      },
      ...
    ]
  }
}
```

> TODO: WHAT if job is not terminated ? 


#### `job_params`

Replaces: `get_test_params`

**Request:**
```json
{
  "jsonrpc": "2.0",
  "method": "job_params",
  "params": {
    "job_id": "Jabc123def456"
  },
  "id": 11
}
```

**Response:**
```json
{
  "jsonrpc": "2.0",
  "result": {
    "domain": "example.com",
    "profile": "default",
    "ds_info": [],
    "nameservers": [],
    "config": {}
  },
  "id": 11
}
```


#### `domain_history` 

Returns a list of completed *jobs* for a domain.

Example request:
```json
{
  "jsonrpc": "2.0",
  "id": 7,
  "method": "domain_history",
  "params": {
    "offset": 0,
    "limit": 200,
    "filter": "all",
    "domain": "zonemaster.net"
  }
}
```

Example response:
```json
{
  "jsonrpc": "2.0",
  "id": 7,
  "result": [
    {
      "job_id": "Jc45a3f8256c4a155",
      "created_at": "2016-11-15T11:53:13Z",
      "started_at": "2016-11-15T12:01:52Z",
      "finished_at": "2016-11-15T13:12:02Z", 
      "undelegated": true,
      "overall_result": "error"
    },
    {
      "job_id": "J32dd4bc0582b6bf9",
      "undelegated": false,
      "created_at": "2016-11-15T11:53:13Z",
      "started_at": "2016-11-15T12:01:52Z",
      "finished_at": "2016-11-15T13:12:02Z", 
      "overall_result": "warning"
    },
    ...
  ]
}
```

>
> Note: The above example response was abbreviated for brevity to only include
> the first two elements in each list. Omitted elements are denoted by a `...`
> symbol.
>

**Undelegated and delegated**

A test is considered to be `"delegated"` below if the test was started, by
[`start_domain_test`][API start_domain_test] or [`add_batch_job`][API add_batch_job]
without specifying neither `"nameserver"` nor `"ds_info"`. Else it is considered to
be `"undelegated"`.

**params**

An object with the following properties:

* `"offset"`: A [*non-negative integer*][Non-negative integer], optional. (default: 0). Position of the first returned element from the database returned list.
* `"limit"`: A [*non-negative integer*][Non-negative integer], optional. (default: 200). Number of element returned from the *offset* element.
* `"filter"`: A string, one of `"all"`, `"delegated"` and `"undelegated"`, optional. (default: `"all"`)
* `"frontend_params"`: An object, required.

The value of "frontend_params" is an object with the following properties:

* `"domain"`: A [*domain name*][Domain name], required.

> TODO: add some time filter ? 

**result**

An object with the following properties:

* `"job_id"` A *job_id*.
* `"created_at"`: A [*timestamp*][Timestamp]. The time in UTC at which the *job* was created.
* `"started_at"`: A [*timestamp*][Timestamp]. The time in UTC at which the *job* was started.
* `"finished_at"`: A [*timestamp*][Timestamp]. The time in UTC at which the *job* was finish.
* `"overall_result"`: A string. It reflects the most severe problem level among
  the test results for the test. It has one of the following values:
  * `"ok"`, if there are only messages with [*severity level*][Severity level] `"INFO"` or
    `"NOTICE"`.
  * `"warning"`, if there is at least one message with [*severity level*][Severity level]
    `"WARNING"`, but none with `"ERROR"` or `"CRITICAL"`.
  * `"error"`, if there is at least one message with [*severity level*][Severity level]
    `"ERROR"`, but none with `"CRITICAL"`.
  * `"critical"`, if there is at least one message with [*severity level*][Severity level]
    `"CRITICAL"`.
* `"undelegated"`: `true` if the test is undelegated, `false` otherwise.

**error**

>
> TODO: List all possible error codes and describe what they mean enough for clients to know how react to them.
>



#### `batch_create`

Replaces: `add_batch_job`  
Related: [#1153](https://github.com/zonemaster/zonemaster-backend/issues/1153), [#1154](https://github.com/zonemaster/zonemaster-backend/issues/1154)

Anonymous users may create batches up to the configured `anonymous_batch_max_size`.  
Authenticated users may create larger batches.  
Batch IDs use the `B` prefix token format.

**Request:**
```json
{
  "jsonrpc": "2.0",
  "method": "batch_create",
  "params": {
    "api_key": "c3CiYYRc1niAPnn6VmXz0AdOGfZnS7Xk",
    "domains": [
      "example.com",
      "example.org",
      "example.net"
    ],
    "profile": "default",
    "params": {}
  },
  "id": 16
}
```

**Response:**
```json
{
  "jsonrpc": "2.0",
  "result": {
    "batch_id": "b_xyz789ghi012"
  },
  "id": 16
}
```

If `api_key` is not provided, anonymous batch feature is used (if `anonymous_batch_enabled` is on) which limite the number of domains that can be processed to `anonymous_batch_max_size`.


**errors**

* bad api key 
* limit exceeded 
* no anonymous_batch_enabled

#### `batch_status`


https://github.com/zonemaster/zonemaster-backend/issues/1156

#TODO improve response  ? by create a object `count` with each ?

**Request:**
```json
{
  "jsonrpc": "2.0",
  "method": "batch_status",
  "params": {
    "batch_id": "Bxyz789ghi012"
  },
  "id": 17
}
```

**Response:**
```json
{
  "jsonrpc": "2.0",
  "result": {
    "batch_id": "Bxyz789ghi012",
    "state": "running",
    "created_at": "2026-07-21T10:00:00Z",
    "waiting_count": 10,
    "running_count": 5,
    "completed_count": 80,
    "cancelled_count": 0,
    "crashed_count": 0,
    "total_count": 95
  },
  "id": 17
}
```

```json
{
  "jsonrpc": "2.0",
  "result": {
    "batch_id": "Bxyz789ghi012",
    "state": "finished",
    "created_at": "2026-07-21T10:00:00Z",
    "finished_at": "2026-07-21T14:38:00Z",
    "waiting_count": 0,
    "running_count": 0,
    "completed_count": 95,
    "cancelled_count": 0,
    "crashed_count": 0,
    "total_count": 95
  },
  "id": 17
}
```

#### `batch_domains`

https://github.com/zonemaster/zonemaster-backend/issues/1151

list jobs for one batch
quesqu'on veux ? 

**Request:**

```json
{
  "jsonrpc": "2.0",
  "method": "batch_domains",
  ￼"params": {
    "batch_id": "Bxyz789ghi012",
    "state": ["waiting", "running", "completed", "cancelled", "crashed"]
  },
  "id": 21
}
```

**Response:**
```json
￼{
  "jsonrpc": "2.0",
  ￼"result": {
    ￼"waiting": {
      "example.org": "Jdef456ghi789"
    },
    ￼"running": {
      "example.net": "Jghi789jkl012"
    },
    ￼"completed": {
      "example.com": "Jabc123def456"
    },
    "cancelled": {},
    "crashed": {}
  },
  "id": 21
}
```

#### `batch_cancel`


New endpoint. Cancels a batch and all its unstarted jobs.  
Related: [#1431](https://github.com/zonemaster/zonemaster/issues/1431)

is batch_id enough security ? if we use id as security we need to increase the size or not
and if we can list batch it s not enough security

**Request:**
```json
{
  "jsonrpc": "2.0",
  "method": "batch_cancel",
  "params": {
    "api_key": "c3CiYYRc1niAPnn6VmXz0AdOGfZnS7Xk",
    "batch_id": "Bxyz789ghi012"
  },
  "id": 22
}
```

**Response:**
```json
{
  "jsonrpc": "2.0",
  "result": {
    "batch_id": "Bxyz789ghi012",
    "state": "cancelled",
    "cancelled_jobs": 15
  },
  "id": 22
}
```

only authenticated batch can be cancelled.

#### `batch_list`

https://github.com/zonemaster/zonemaster/issues/1430

listing batchs include security concerne,
- do we see all batchs
- do we see our batchs

**Request:**
```json
{
  "jsonrpc": "2.0",
  "method": "batch_list",
  "params": {
    "api_key": "c3CiYYRc1niAPnn6VmXz0AdOGfZnS7Xk",
    "state": ["waiting", "running", "completed"],
    "offset": 0,
    "limit": 50
  },
  "id": 20
}
```

**Response:**
```json
{
  "jsonrpc": "2.0",
  "result": {
    "batches": [
      {
        "batch_id": "b_xyz789ghi012",
        "state": "running",
        "created_at": "2026-07-21T10:00:00Z",
        "started_at": "2026-07-21T10:23:00Z",
        "total_count": 95,
        "waiting_count": 10,
        "running_count": 5,
        "completed_count": 80
      },

    ],
    "total": 12,
    "offset": 0,
    "limit": 50
  },
  "id": 20
}
```
#TODO# list des batches n'est pas consistant avec la liste des batch_jobs 

#### `message_explanation`

**Request:**
```json
{
  "jsonrpc": "2.0",
  "method": "message_explanation",
  "params": {
    "module": "Basic",
    "testcase": "basic01",
    "tag": "BASIC01_QUERY_RESPONSE",
    "language": "en"
  },
  "id": 27
}
```

**Response:**
```json
{
  "jsonrpc": "2.0",
  "result": {
    "module": "Basic",
    "testcase": "basic01",
    "tag": "BASIC01_QUERY_RESPONSE",
    "description": "The name server responded to the query without errors.",
    "level": "INFO",
    "reference_url": "https://doc.zonemaster.net/latest/specifications/tests/Basic-TP/basic01.md"
  },
  "id": 27
}
```


#### `statistics_overview`

is this precomputed statistique or can we ask for more complicated statistique ?

**Request:**
```json
{
  "jsonrpc": "2.0",
  "method": "statistics_overview",
  "params": {},
  "id": 29
}
```

**Response:**
```json
{
  "jsonrpc": "2.0",
  "result": {
    "total_jobs": 150000,
    "total_batches": 5000,
    "jobs_today": 250,
    "jobs_this_week": 1500,
    "active_batches": 12,
    "queue_depth": 45
  },
  "id": 29
}
```
