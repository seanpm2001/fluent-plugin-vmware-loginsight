# fluent-plugin-vmware-loginsight

## Overview
output plugin to do forward logs to VMware Log Insight

## Installation

### RubyGems

```
$ gem install fluent-plugin-vmware-loginsight
```

### Bundler

Add following line to your Gemfile:

```ruby
gem "fluent-plugin-vmware-loginsight"
```

And then execute:

```
$ bundle
```

## Usage

```
<source>
  @type tail
  path /var/log/containers/*.log
  pos_file /var/log/fluentd-docker.pos
  time_format %Y-%m-%dT%H:%M:%S
  tag kubernetes.*
  format json
  read_from_head true
</source>

# Kubernetes metadata filter that tags additional meta data for each event
<filter kubernetes.var.log.containers.**.log>
  @type kubernetes_metadata
</filter>

<match **>
  @type vmware_loginsight
  scheme https
  ssl_verify true
# Loginsight host: One may use IP address or cname
# host X.X.X.X
  host my-loginsight.mycompany.com
  port 9000
  path api/v1/events/ingest
  agent_id XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
  http_method post
  serializer json
  rate_limit_msec 0
  raise_on_error false
  include_tag_key true
  tag_key tag
</match>
```
### Configuration options

```
scheme, :string, :default => 'http' :: Valid Values: http/https 

# Loginsight Host ex. localhost
host, :string,  :default => 'localhost' :: Valid Values: loginsight_url | loginsight_ip

# Loginsight port ex. 9000
port, :integer, :default => 80

# Loginsight ingestion api path ex. 'api/v1/events/ingest'
path, :string, :default => 'api/v1/events/ingest'

# agent_id generated by your LI
agent_id, :string, :default => '0'

# Credentials if used
username, :string, :default => nil
password, :string, :default => nil,

# Authentication
authentication, :string, :default => nil :: Valid Value: nil | basic

# SSL verification flag
ssl_verify, :bool, :default => true :: Valid Value: true | false
# CA Cert filep
ca_file, :string, :default => nil

# HTTP method
http_method, :string, :default => :post :: Valid Value: post

# Serialization
serializer, :string, :default => :json :: Valid Value: json

# Number of retries
request_retries, :integer, :default => 3
# http connection ttl for each request
request_timeout, :time, :default => 5

# If set, enables debug logs for http connection
http_conn_debug, :bool, :default => false :: Valid Value: true | false

# Simple rate limiting: ignore any records within `rate_limit_msec` since the last one
rate_limit_msec, :integer, :default => 0

# Raise errors that were rescued during HTTP requests?
raise_on_error, :bool, :default => false :: Valid Value: true | false 

# Include tag key as log event?
include_tag_key, :bool, :default => true :: Valid Value: true | false 

# Metadata key that identifies Fluentd tags
tag_key, :string, :default => 'tag'

# Keys from log event whose values should be added as log message/text to loginsight.
# These key/value pairs won't expanded/flattened and won't be added as metadata/fields.
log_text_keys, :array, :default => ["log", "message", "msg"] :: Valid Value: Array of strings

# Flatten hashes to create one key/val pair w/o losing log data
flatten_hashes, :bool, :default => true :: Valid Value: true | false

# Seperator to use for joining flattened keys
flatten_hashes_separator, :string, :default => "_"
```

For more examples look at [examples](./examples/)

## Contributing

The fluent-plugin-vmware-loginsight project team welcomes contributions from the community. Before you start working with fluent-plugin-vmware-loginsight, please read our [Developer Certificate of Origin](https://cla.vmware.com/dco). All contributions to this repository must be signed as described on that page. Your signature certifies that you wrote the patch or have the right to pass it on as an open-source patch. For more detailed information, refer to [CONTRIBUTING.md](CONTRIBUTING.md).

## License
Fluentd plugin for VMware Log Insight

Copyright 2018 VMware, Inc. All Rights Reserved. 

This product is licensed to you under the MIT license (the "License").  You may not use this product except in compliance with the MIT License.  

This product may include a number of subcomponents with separate copyright notices and license terms. Your use of these subcomponents is subject to the terms and conditions of the subcomponent's license, as noted in the LICENSE file. 

SPDX-License-Identifier: MIT
