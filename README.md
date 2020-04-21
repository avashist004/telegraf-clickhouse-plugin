# Telegraf output plugin 
-----

**This is not an official Paratera product**

## 1. How to use

Binary for the Telegraf needs to be created from source in order to use the telegraf clickhouse plugin.
The steps below are described in reference with CentOS 7.

**PreRequisite**

1. Install Go >=1.9 (1.10 recommended).
2. Set following paths in file ~/.bash_profile: 
```bash
export GOROOT=/usr/local/go
export PATH=$PATH:/usr/local/go/bin
export GOBIN="/usr/local/go/bin"
export GOPATH="$HOME/go"
```
3. Reload the path using command: 
```bash
source /etc/profile && source ~/.bash_profile
```
4. Install dep ==v0.5.0

5. Download Telegraf source: 
```bash
# go get -d github.com/influxdata/telegraf
```
6. Telegraf source should be present under directory:
```bash
$HOME/go/src/github.com/influxdata/telegraf
```


## 2. Adding plugin files to telegraf repository and bulding Telegraf binary.

2.1. Go to directory: $HOME/go/src/github.com/influxdata/telegraf
```bash
# cd $HOME/go/src/github.com/influxdata/telegraf
```
2.2. Create a directory for the ClickHouse output plugin

```bash
# mkdir -p telegraf/plugins/outputs/clickhouse
```

2.3. Copy the files client.go, metrics.go and register.go to clickhouse output plugin directory.

```bash
# cp client.go metrics.go register.go telegraf/plugins/outputs/clickhouse
```

2.4. Enable this plugin
Append plugin into plugins/outputs/all/all.go
```bash
        _ "github.com/influxdata/telegraf/plugins/outputs/socket_writer"
        _ "github.com/influxdata/telegraf/plugins/outputs/stackdriver"
        _ "github.com/influxdata/telegraf/plugins/outputs/wavefront"
+       _ "github.com/influxdata/telegraf/plugins/outputs/clickhouse"
)
```
2.5. build telegraf 

```bash
# cd $HOME/go/src/github.com/influxdata/telegraf
# make
```
Above command should generate the telegraf binary under telegraf directory.

2.6. Copy the telegraf binary generated to path **/usr/bin**.

2.7. Create the telegraf service (not necessary but recommended):
Reference link to create service: https://www.linode.com/docs/quick-answers/linux/start-service-at-boot/

Sample configurations for Telegraf service: https://github.com/influxdata/telegraf/blob/master/scripts/telegraf.service


## 3. update telegraf.conf
append follow lines to the telegraf.conf file to enable the ClickHouse output plugin:

```ini
[[outputs.clickhouse]]
	user = "default"
	password = ""
        addr = "127.0.0.1"
        port = 9000
        database = "telegraf"
        tablename = "metrics"
	write_timeout = 10
	debug = false
```

