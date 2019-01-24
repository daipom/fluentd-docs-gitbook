# Centralize Logs from PHP Applications

The '[fluent-logger-php](http://github.com/fluent/fluent-logger-php)'
library is used to post records from PHP applications to Fluentd.

This article explains how to use the fluent-logger-php library.


## Prerequisites

-   Basic knowledge of PHP
-   Basic knowledge of Fluentd
-   PHP 5.3 or higher

## Installing Fluentd

Please refer to the following documents to install fluentd.

-   [Install Fluentd with rpm Package](/articles/install-by-rpm.md)
-   [Install Fluentd with deb Package](/articles/install-by-deb.md)
-   [Install Fluentd with Ruby Gem](/articles/install-by-gem.md)
-   [Install Fluentd from source](/articles/install-from-source.md)

## Modifying the Config File

Next, please configure Fluentd to use the [forward Input plugin](/plugins/input/forward.md) as its data source.

``` {.CodeRay}
# Unix Domain Socket Input
<source>
  @type unix
  path /var/run/td-agent/td-agent.sock
</source>
<match fluentd.test.**>
  @type stdout
</match>
```

Please restart your agent once these lines are in place.

``` {.CodeRay}
# for rpm/deb only
$ sudo /etc/init.d/td-agent restart
```

## Using fluent-logger-php

To use fluent-logger-php, copy the library into your project directory.

``` {.CodeRay}
$ git clone https://github.com/fluent/fluent-logger-php.git
$ cp -r src/Fluent <path/to/your_project>
```

Next, initialize and post the records as shown below.

``` {.CodeRay}
<?php
require_once __DIR__.'/src/Fluent/Autoloader.php';
use Fluent\Logger\FluentLogger;
Fluent\Autoloader::register();
$logger = new FluentLogger("unix:///var/run/td-agent/td-agent.sock");
$logger->post("fluentd.test.follow", array("from"=>"userA", "to"=>"userB"));
```

Executing the script will send the logs to Fluentd.

``` {.CodeRay}
$ php test.php
```

The logs should be output to `/var/log/td-agent/td-agent.log` or stdout
of the Fluentd process via the [stdout Output plugin](/plugins/output/stdout.md).

## Production Deployments

### Output Plugins

Various [output plugins](/plugins/output/README.md) are available for
writing records to other destinations:

-   Examples
    -   [Store Apache Logs into Amazon S3](/articles/apache-to-s3.md)
    -   [Store Apache Logs into MongoDB](/articles/apache-to-mongodb.md)
    -   [Data Collection into HDFS](/articles/http-to-hdfs.md)
-   List of Plugin References
    -   [Output to Another Fluentd](/plugins/output/forward.md)
    -   [Output to MongoDB](/plugins/output/mongo.md) or [MongoDB ReplicaSet](/plugins/output/mongo_replset.md)
    -   [Output to Hadoop](/plugins/output/webhdfs.md)
    -   [Output to File](/plugins/output/file.md)
    -   [etc...](http://fluentd.org/plugin/)

### High-Availability Configurations of Fluentd

For high-traffic websites (more than 5 application nodes), we recommend
using a high availability configuration of td-agent. This will improve
data transfer reliability and query performance.

-   [High-Availability Configurations of Fluentd](/deployment/high-availability.md)

### Monitoring

Monitoring Fluentd itself is also important. The article below describes
general monitoring methods for td-agent.

-   [Monitoring Fluentd](/deployment/monitoring.md)


------------------------------------------------------------------------

If this article is incorrect or outdated, or omits critical information, please [let us know](https://github.com/fluent/fluentd-docs/issues?state=open).
[Fluentd](http://www.fluentd.org/) is a open source project under [Cloud Native Computing Foundation (CNCF)](https://cncf.io/). All components are available under the Apache 2 License.