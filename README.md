yii-log
=======

[![Build Status](https://travis-ci.org/index0h/yii-log.png?branch=master)](https://travis-ci.org/index0h/yii-log) [![Latest Stable Version](https://poser.pugx.org/index0h/yii-log/v/stable.png)](https://packagist.org/packages/index0h/yii-log) [![Dependency Status](https://gemnasium.com/index0h/yii-log.png)](https://gemnasium.com/index0h/yii-log) [![Scrutinizer Quality Score](https://scrutinizer-ci.com/g/index0h/yii-log/badges/quality-score.png?s=9d7c3843dedbc78e4229dedae09fb2eff72c4012)](https://scrutinizer-ci.com/g/index0h/yii-log/) [![Code Coverage](https://scrutinizer-ci.com/g/index0h/yii-log/badges/coverage.png?s=e5fe3d9d1ff40c1e01fbb9b91068b34ad74a78fe)](https://scrutinizer-ci.com/g/index0h/yii-log/)    [![Total Downloads](https://poser.pugx.org/index0h/yii-log/downloads.png)](https://packagist.org/packages/index0h/yii-log)    [![License](https://poser.pugx.org/index0h/yii-log/license.png)](https://packagist.org/packages/index0h/yii-log)

Different Yii2 log transports

## Now available

* ElasticsearchTarget
* LogstashFileTarget
* LogstashTarget
* RedisTarget

## Installation

The preferred way to install this extension is through [composer](http://getcomposer.org/download/).

```sh
php composer.phar require --prefer-dist index0h/yii-log "0.0.1"
```

or add line to require section of `composer.json`

```json
"index0h/yii-log": "0.0.1"
```

## Usage

### Common properties

* `$emergencyLogFile`, default `@app/logs/logService.log`

Elasticsearch, Redis and Logstash - are external services, so if they down our logs must be stored in file.
For that **ElasticsearchTarget**, **LogstashTarget**, **RedisTarget** have option `$emergencyLogFile`. It's alias to
file where logs will be written on log service is down.

### ElasticsearchTarget

Stores logs in elasticsearch. All logs transform to json, so you can simply watch them by [kibana](http://www.elasticsearch.org/overview/kibana/).

Extends yii\log\Target, more options see [here](https://github.com/yiisoft/yii2/blob/master/framework/log/Target.php)

##### Example configuration

```php
....
'components' => [
    'log' => [
        'targets' => [
            ['class' => 'index0h\\yii\\log\\ElasticsearchTarget'],
        ....
```

##### Properties

* `dsn`, default 'http://localhost:9200/yii/log' - URL to elasticsearch service. `yii` - _index, `log` - _type.

### LogstashFileTarget

Extends yii\log\FileTarget, more options see [here](https://github.com/yiisoft/yii2/blob/master/framework/log/FileTarget.php)

##### Example Yii configuration

```php
....
'components' => [
    'log' => [
        'targets' => [
            ['class' => 'index0h\\yii\\log\\LogstashFileTarget'],
        ....
```

##### Example Logstash configuration [(current version 1.3.3)](http://logstash.net/docs/1.3.3/)

```
input {
  file {
    type => "yii_log"
    path => [ "path/to/yii/logs/*.log*" ]
    start_position => "end"
    stat_interval => 1
    discover_interval => 30
    codec => "json"
  }
}

filter {
  # ...
}

output {
  stdout => {}
}
```

### LogstashTarget

Extends yii\log\Target, more options see [here](https://github.com/yiisoft/yii2/blob/master/framework/log/Target.php)

##### Example Yii configuration

```php
....
'components' => [
    'log' => [
        'targets' => [
            ['class' => 'index0h\\yii\\log\\LogstashTarget'],
            // Or UDP.
            [
                'class' => 'index0h\\yii\\log\\LogstashTarget',
                'dsn' => 'udp://localhost:3333'
            ],
            // Or unix socket file.
            [
                'class' => 'index0h\\yii\\log\\LogstashTarget',
                'dsn' => 'unix:///path/to/logstash.sock'
            ],
        ....
```

##### Example Logstash configuration [(current version 1.3.3)](http://logstash.net/docs/1.3.3/)

```
input {
  tcp {
    type => "yii_log"
    port => 3333
    codec => "json"
  }
  # Or UDP.
  udp {
    type => "yii_log"
    port => 3333
    codec => "json"
  }
  # Or unix socket file.
  unix {
    type => "yii_log"
    path => "path/to/logstash.sock"
    codec => "json"
  }
}

filter {
  # ...
}

output {
  stdout => {}
}
```


##### Properties

* `dsn`, default `tcp://localhost:3333` - URL to logstash service. Allowed schemas:
    **tcp**, **udp**, **unix** - for unix sock files.

### RedisTarget

Extends yii\log\Target, more options see [here](https://github.com/yiisoft/yii2/blob/master/framework/log/Target.php)

##### Example Yii configuration

```php
....
'components' => [
    'log' => [
        'targets' => [
            ['class' => 'index0h\\yii\\log\\RedisTarget'],
        ....
```

##### Example Logstash configuration [(current version 1.3.3)](http://logstash.net/docs/1.3.3/)

```
input {
  redis {
    type => "yii_log"
    key => "yii_log"
    codec => "json"
  }
}

filter {
  # ...
}

output {
  stdout => {}
}
```

##### Properties

* `key`, default `yii_log` - Redis list key.
* `options` - Predis client options, [see docs](https://github.com/nrk/predis#connecting-to-redis).
* `parameters` - Predis client parameters, [see docs](https://github.com/nrk/predis#connecting-to-redis).

## Testing

#### Run tests from IDE (example for PhpStorm)

- Select Run/Debug Configuration -> Edit Configurations
- Select Add New Configuration -> PHP Script
- Type:
    * File: /path/to/yii-phar/runTests.php
    * Arguments run: run  --coverage --html
- OK

#### Run tests from console

```sh
make test
```