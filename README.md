# MQTTui

## About

This TUI application is used to configure and install an MQTT data collection, storage and access suite.
The suite requires you to configure a few things:
1. Database path
 - By default, if left unconfigured, the database path will be where the MQTTui application was run from.
2. Broker IP
 - By default this will be localhost, meaning the data host and broker are the same device.
3. Base topic
 - By default this is '/' meaning all topics will be matched.

The suite consists of two programs currently:

1. [Sub Store](https://github.com/GerhardusC/SubStore):
Data is collected matching all paths over the base topic and saved to a local database. This database is a simple sqlite db.
If data published can be parsed as a number, it will be saved to the 'Measurements' table in the database, otherwise, it is saved
into the 'Logs' table.

2. [Data Dashboard](https://github.com/GerhardusC/data_dashboard)
A simple REST api serves the data from the SQLite db. You can select data in a range, or all data. Viewing the DB logs using this REST api is not yet implemented. Additionally, the API will try to serve a page to the base URL that is a really simple visualisation of the data.
If you would like to see the data in more detail, prefer to set up something like Grafana with the SQLite plugin to view the data.

## Installation

### Dependencies

Though the Sub Store and Data Dashboard themselves can be installed with no dependencies, this TUI application has a couple of dependencies.
The TUI currently depends on the `unzip`, `dbus` and `systemd`. The dependency on unzip is to be removed in a future version, but the dependencies on systemd and dbus are here to stay.

You may also choose to install Redis, as the SubStore application will also unintrusively attempt to save the instantaneous value of each topic in the respective key in a redis store.

### Installation methods

#### Build from source
Currently this is the only way to run the application. I may decide to create a workflow one day to build this program and upload a release, but I have not yet had time to do this.

- Run `cargo build --release`

From here you can copy the output file (`./target/release/mqttui`) to wherever to run.

## Usage

NOTE: This program must be run with administrator privilages to interact correctly with systemd through dbus.

Run the mqttui program with the following args:
  -d, --db-path <DB_PATH>      Path to database [default: ./data.db]
  -b, --broker-ip <BROKER_IP>  Initial host to connect to via mqtt [default: localhost]
  -t, --topic <TOPIC>          Initial topic to subscribe to via mqtt [default: /#]
  -h, --help                   Print help
  -V, --version                Print version


### Pages

The TUI consists of three pages, one page to set up the Sub Store and Data Dashboard daemons to ensure they run at startup and point to the correct and same database, one page to tail live logs for any mqtt topic, and a final page to view and delete values in the measurements and logs tables.

#### Configuration
On the configuration page you can set up the relevant services to run at startup. Installing a service will go to the releases page of the GitHub project and download the zip release created in the last github action. Currently only supporting x86 build, but this also might change in future. Here you may also choose to uninstall or remove the services again.

#### Live logs
On this page you may view messages coming in live to any topic of your choosing and of any host of your choosing.

#### Table explorer
On this page you can simply view the values of the 'measurements' and 'logs' tables in the sqlite database. It just allows you to delete specific measurements by their topic and timestamp. This page is clunky to use for deleting many measurements, if you need to do anything substantial, I really recommend just using the sqlite db directly.

