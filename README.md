> This repository is part of the [Pelias](https://github.com/pelias/pelias) project. Pelias is an open-source, open-data geocoder built by [Mapzen](https://www.mapzen.com/) that also powers [Mapzen Search](https://mapzen.com/projects/search). Our official user documentation is [here](https://mapzen.com/documentation/search/).

**Work In Progress**

# Polyline importer

![Travis CI Status](https://travis-ci.org/pelias/polylines.svg)
[![Gitter Chat](https://badges.gitter.im/pelias/pelias.svg)](https://gitter.im/pelias/pelias?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

The polyline importer facilitates importing road network data in to Pelias from a list of [polyline encoded](https://developers.google.com/maps/documentation/utilities/polylinealgorithm) line strings.

## Prerequisites

* NodeJS `4` or newer (the latest in the Node 4 series is currently recommended)
* Elasticsearch 2.3+ (support for version 1.x has been deprecated).

## Clone and Install dependencies

Since this module is just one part of our geocoder, we'd recommend starting with our [Vagrant image](https://github.com/pelias/vagrant) for quick setup, or our [full installation docs](https://github.com/pelias/pelias-doc/blob/master/installing.md) to use this module.

```bash
$ git clone https://github.com/pelias/polylines.git && cd polylines;
$ npm install
```

## Download data

We are still deciding on the best format to publish polyline data for distribution.

Currently there is a single planet-wide road network file which was cut on 8th Jan 2017, [download here](http://missinglink.files.s3.amazonaws.com/road_network.gz) (1.5GB compressed, 2.3GB uncompressed).

For more information on how the extract was generated, see the wiki article: [Generating polylines from Valhalla](https://github.com/pelias/polylines/wiki/Generating-polylines-from-Valhalla).

We also have some smaller extracts for testing purposes, a small number were manually cut from pbf for the geographies of our major contributors, open an issue if you would like another geography listed:

**note:** these extracts were generated using a different method from the planet cut above.

- [Berlin](http://missinglink.files.s3.amazonaws.com/berlin.gz) (1.9MB, 49k roads)
- [New York](http://missinglink.files.s3.amazonaws.com/new_york.gz) (4.2MB, 102k roads)
- [Finland](http://missinglink.files.s3.amazonaws.com/finland.gz) (7.7MB, 100k roads)
- [Sweden](http://missinglink.files.s3.amazonaws.com/sweden.gz) (5.9MB, 126k roads)
- [London](http://missinglink.files.s3.amazonaws.com/london.gz) (5.6MB, 166k roads)
- [Paris](http://missinglink.files.s3.amazonaws.com/paris.gz) (2.9MB, 81k roads)
- [San Francisco](http://missinglink.files.s3.amazonaws.com/san_francisco.gz) (1.3MB, 27k roads)
- [New Zealand](http://missinglink.files.s3.amazonaws.com/new_zealand.gz) (3.1MB, 52k roads)

Once you have downloaded and extracted the data you will need to follow the *Configuration* steps below in order to tell Pelias where they can be found.

If you would like to use a different source of polyline data you might need to tweak the defaults in `./stream/pipeline.js`, open an issue if you get stuck.

## Configuration

In order to tell the importer the location of your downloads and environmental settings you will first need to create a `~/pelias.json` file.

See [the config](https://github.com/pelias/config) documentation for details on the structure of this file. Your relevant config info for the polyline module might look something like this:

**note:** the importer currently only supports a single entry in the `files` array.

```javascript
  "imports": {
    "polyline": {
      "adminLookup": true,
      "datapath": "/data",
      "files": [ "road_network.polylines" ]
    }
  }
```

### Administrative Hierarchy Lookup

Polyline data doesn't have a full administrative hierarchy (ie, country, state,
county, etc. names), but it can be calculated using data from [Who's on
First](http://whosonfirst.mapzen.com/). See the [readme](https://github.com/pelias/wof-admin-lookup/blob/master/README.md)
for [pelias/wof-admin-lookup](https://github.com/pelias/wof-admin-lookup) for more information.

Set the `imports.polyline.adminLookup` property in `pelias.json` to `true` to enable admin lookup.

## Running an import

This will start the import process, it will take around 30 seconds to prime it's in-memory data and then you should see regular debugging output in the terminal.

```bash
$ PELIAS_CONFIG=<path_to_config_json> npm start
```

## CLI tool

You can use the CLI tool to run imports and for debugging purposes:

**note:** by default the cli tool will read from `stdin` and write to `stdout`.

```bash
$ node ./bin/cli.js --help
Usage: cli.js [options]
Options:

  --file           read from file instead of stdin
  --config         read filename from pelias config (overrides --file)
  --pretty         indent output (stdout only)
  --db             save to elasticsearch instead of printing to stdout

```

### Examples

Run a 'dry-run' of the import process:

```bash
node ./bin/cli.js --config --pretty
```

Import a specific file to elasticsearch:

```bash
node ./bin/cli.js --file=/tmp/myfile.polylines --db
```

## Issues

If you have any issues getting set up or the documentation is missing something, please open an issue here: https://github.com/pelias/polylines/issues

## Contributing

Please fork and pull request against upstream master on a feature branch.

Pretty please; provide unit tests and script fixtures in the `test` directory.

## Code Linting

A `.jshintrc` file is provided which contains a linting config, usually your text editor will understand this config and give you inline hints on code style and readability.

These settings are strictly enforced when you do a `git commit`, you can execute `git commit` at any time to run the linter against your code.

### Running Unit Tests

```bash
$ npm test
```

### Continuous Integration

Travis tests every change against Node.js 4` and `6`.
