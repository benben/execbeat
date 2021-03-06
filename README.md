[![Build Status](https://travis-ci.org/christiangalsterer/execbeat.svg?branch=master)](https://travis-ci.org/christiangalsterer/execbeat)
[![codecov.io](http://codecov.io/github/christiangalsterer/execbeat/coverage.svg?branch=master)](http://codecov.io/github/christiangalsterer/execbeat?branch=master)
[![Go Report Card](https://goreportcard.com/badge/github.com/christiangalsterer/execbeat)](https://goreportcard.com/report/github.com/christiangalsterer/execbeat)

# Overview

Execbeat is the [Beat](https://www.elastic.co/products/beats) used to execute any command.
Multiple commands can be configured which are executed in a regular interval and the standard output and standard error is shipped to the configured output channel.

Execbeat is inspired by the Logstash [exec](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-exec.html) input filter but doesn't require that the endpoint is reachable by Logstash as Execbeat pushes the data to Logstash or Elasticsearch.
This is often necessary in security restricted network setups, where Logstash is not able to reach all servers. Instead the server to be monitored itself has Execbeat installed and can send the data or a collector server has Execbeat installed which is deployed in the secured network environment and can reach all servers to be monitored.

Ensure that this folder is at the following location:
`${GOPATH}/src/github.com/christiangalsterer`

## Getting Started with Execbeat

### Requirements

* [Golang](https://golang.org/dl/) 1.7.1
* [Glide](https://github.com/Masterminds/glide) >= 0.11.0

### Build

To build the binary for execbeat run the command below. This will generate a binary
in the same directory with the name execbeat.

```
make clean && make
```


### Run

To run execbeat with debugging output enabled, run:

```
./execbeat -c execbeat.yml -e -d "*"
```


### Test

To test execbeat, run the following command:

```
make testsuite
```

alternatively:
```
make unit-tests
make system-tests
make integration-tests
make coverage-report
```

The test coverage is reported in the folder `./build/coverage/`

### Update

Each beat has a template for the mapping in elasticsearch and a documentation for the fields
which is automatically generated based on `etc/fields.yml`.
To generate etc/execbeat.template.json and etc/execbeat.asciidoc

```
make update
```


### Cleanup

To clean execbeat source code, run the following commands:

```
make fmt
make simplify
```

To clean up the build directory and generated artifacts, run:

```
make clean
```


### Clone

To clone execbeat from the git repository, run the following commands:

```
mkdir -p ${GOPATH}/github.com/christiangalsterer
cd ${GOPATH}/github.com/christiangalsterer
git clone https://github.com/christiangalsterer/execbeat
```


For further development, check out the [beat developer guide](https://www.elastic.co/guide/en/beats/libbeat/current/new-beat.html).


## Packaging

The beat frameworks provides tools to crosscompile and package your beat for different platforms. This requires [docker](https://www.docker.com/) and vendoring as described above. To build packages of your beat, run the following command:

```
make package
```

This will fetch and create all images required for the build process. The complete process to finish can take several minutes.

# Releases

2.0.0 (2016-11-26)

Feature release containing the following changes:
* Update to beats v5.0.1

Please note that this release contains the following breaking changes introduced by beats 5.0.X, see also [Beats Changelog](https://github.com/elastic/beats/blob/v5.0.0-beta1/CHANGELOG.asciidoc)
* SSL Configuration
    * rename tls configurations section to ssl
    * rename certificate_key configuration to key.
    * replace tls.insecure with ssl.verification_mode setting.
    * replace tls.min/max_version with ssl.supported_protocols setting requiring full protocol name

1.1.0 (2016-07-19)

Feature release containing the following changes:
* Update to Go 1.6
* Update to libbeat 1.2.3
* Use [Glide](https://github.com/Masterminds/glide) for dependency management

1.0.1 (2016-02-15)

Bugfix release containing the following changes:
* Fix: [Hanging during shutdown](https://github.com/christiangalsterer/execbeat/issues/2)

1.0.0 (2015-12-26)
* Initial release

# Configuration

## Configuration Options

See [here](docs/configuration.asciidoc) for more information.

## Exported Document Types

There is exactly one document type exported:

- `type: execbeat` command execution information, e.g. standard output and standard error. The type can be changed by setting the document_type attribute.

## Exported Fields

See [here](docs/fields.asciidoc) for a detailed description of all exported fields.

### execbeat type

<pre>
{
  "_index": "execbeat-2015.12.26",
  "_type": "execbeat",
  "_source": {
    "@timestamp": "2015-12-26T02:18:53.001Z",
    "beat": {
      "hostname": "mbp.box",
      "name": "mbp.box"
    },
    "count": 1,
    "fields": {
      "host": "test"
    },
    "exec": {
      "command": "echo",
      "stdout": "Hello World\n"
    },
    "fields": {
      "host": "test2"
    },
    "type": "execbeat"
    },
  "sort": [
    1449314173
  ]
}
</pre>


## Elasticsearch Template

To apply the Execbeat template:

    curl -XPUT 'http://localhost:9200/_template/execbeat' -d@etc/execbeat.template.json

# Contribution
All sorts of contributions are welcome. Please create a pull request and/or issue.
