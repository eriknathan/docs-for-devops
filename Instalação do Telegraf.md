# Instalação do Telegraf

## O que é?

O Telegraf é um agente Open Source que nos ajuda a coletar métricas de praticamente tudo o que quisermos. Tem suporte para mais de 100 plugins de coleta de dados.

Segue abaixo uma imagem demonstrando o funcionamento básico do Telegraf:

## Instalação

Para instala o Telegraf no Ubuntu basta executar os comandos:

```bash
curl -s https://repos.influxdata.com/influxdata-archive.key > influxdata-archive.key
echo '943666881a1b8d9b849b74caebf02d3465d6beb716510d86a39f6c8e8dac7515 influxdata-archive.key' | sha256sum -c && cat influxdata-archive.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/influxdata-archive.gpg > /dev/null
echo 'deb [signed-by=/etc/apt/trusted.gpg.d/influxdata-archive.gpg] https://repos.influxdata.com/debian stable main' | sudo tee /etc/apt/sources.list.d/influxdata.list
sudo apt-get update && sudo apt-get install telegraf
```

Em versões mais antigas do Ubuntu pode ocorrer o erro abaixo:

```bash
Err:4 https://repos.influxdata.com/ubuntu xenial InRelease
  The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 684A14CF2582E0C5
```

Esse erro pode ser resolvido adicionando a key com o comando:

```bash
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 684A14CF2582E0C5
```

## Configuração

O arquivo de configuração do telegraf fica localizado no caminho: **/etc/telegraf/telegraf.conf**

O arquivo de configuração é separa basicamente em 3 seções. Sendo a primeira o [agent] que contém as configurações básicas das métricas de coletas de dados. Como por exemplo: Intervalo das coletas; localização do arquivo de log; timezone; hostname e etc.

[agent] - Abaixo segue um exemplo de configuração da seção:

```bash
[agent]
  ## Default data collection interval for all inputs
  interval = "10s"
  ## Rounds collection interval to 'interval'
  ## ie, if interval="10s" then always collect on :00, :10, :20, etc.
  round_interval = true

  ## Telegraf will send metrics to outputs in batches of at most
  ## metric_batch_size metrics.
  ## This controls the size of writes that Telegraf sends to output plugins.
  metric_batch_size = 1000

  ## Maximum number of unwritten metrics per output.  Increasing this value
  ## allows for longer periods of output downtime without dropping metrics at the
  ## cost of higher maximum memory usage.
  metric_buffer_limit = 10000

  ## Collection jitter is used to jitter the collection by a random amount.
  ## Each plugin will sleep for a random time within jitter before collecting.
  ## This can be used to avoid many plugins querying things like sysfs at the
  ## same time, which can have a measurable effect on the system.
  collection_jitter = "0s"

  ## Default flushing interval for all outputs. Maximum flush_interval will be
  ## flush_interval + flush_jitter
  flush_interval = "10s"
  ## Jitter the flush interval by a random amount. This is primarily to avoid
  ## large write spikes for users running a large number of telegraf instances.
  ## ie, a jitter of 5s and interval 10s means flushes will happen every 10-15s
  flush_jitter = "0s"

  ## By default or when set to "0s", precision will be set to the same
  ## timestamp order as the collection interval, with the maximum being 1s.
  ##   ie, when interval = "10s", precision will be "1s"
  ##       when interval = "250ms", precision will be "1ms"
  ## Precision will NOT be used for service inputs. It is up to each individual
  ## service input to set the timestamp at the appropriate precision.
  ## Valid time units are "ns", "us" (or "µs"), "ms", "s".
  precision = ""

  ## Log at debug level.
  # debug = false
  ## Log only error level messages.
  # quiet = false

  ## Log target controls the destination for logs and can be one of "file",
  ## "stderr" or, on Windows, "eventlog".  When set to "file", the output file
  ## is determined by the "logfile" setting.
  # logtarget = "file"

  ## Name of the file to be logged to when using the "file" logtarget.  If set to
  ## the empty string then logs are written to stderr.
  logfile = "/var/log/telegraf/telegraf.log"

  ## The logfile will be rotated after the time interval specified.  When set
  ## to 0 no time based rotation is performed.  Logs are rotated only when
  ## written to, if there is no log activity rotation may be delayed.
  # logfile_rotation_interval = "0d"

  ## The logfile will be rotated when it becomes larger than the specified
  ## size.  When set to 0 no size based rotation is performed.
  # logfile_rotation_max_size = "0MB"

  ## Maximum number of rotated archives to keep, any older logs are deleted.
  ## If set to -1, no archives are removed.
  # logfile_rotation_max_archives = 5

  ## Pick a timezone to use when logging or type 'local' for local time.
  ## Example: America/Chicago
  log_with_timezone = "America/Recife"

  ## Override default hostname, if empty use os.Hostname()
  hostname = "sgt-master"
  ## If set to true, do no set the "host" tag in the telegraf agent.
  omit_hostname = false
```

[outputs] - A segunda seção é a output onde configuramos para onde os dados coletados serão enviados. É nela que iremos colocar os valores de host, organização, bucket do InfluxDB e o token.

> O TOKEN de autentição é criado no InfluxDB. Consulte a documentação do InfluxDB para mais detalhes.

Abaixo temos um exemplo de código:

```bash
[[outputs.influxdb_v2]]
  ## The URLs of the InfluxDB cluster nodes.
  ##
  ## Multiple URLs can be specified for a single cluster, only ONE of the
  ## urls will be written to each interval.
  ##   ex: urls = ["https://us-west-2-1.aws.cloud2.influxdata.com"]
  urls = ["https://watchdb.isitics.com"]

  ## Token for authentication.
  token = "insert_token_here"

  ## Organization is the name of the organization you wish to write to; must exist.
  organization = "isitics"

  ## Destination bucket to write into.
  bucket = "sgt"

  ## The value of this tag will be used to determine the bucket.  If this
  ## tag is not set the 'bucket' option is used as the default.
  # bucket_tag = ""

  ## If true, the bucket tag will not be added to the metric.
  # exclude_bucket_tag = false

  ## Timeout for HTTP messages.
  # timeout = "5s"

  ## Additional HTTP headers
  # http_headers = {"X-Special-Header" = "Special-Value"}

  ## HTTP Proxy override, if unset values the standard proxy environment
  ## variables are consulted to determine which proxy, if any, should be used.
  # http_proxy = "http://corporate.proxy:3128"

  ## HTTP User-Agent
  # user_agent = "telegraf"

  ## Content-Encoding for write request body, can be set to "gzip" to
  ## compress body or "identity" to apply no encoding.
  # content_encoding = "gzip"

  ## Enable or disable uint support for writing uints influxdb 2.0.
  # influx_uint_support = false

  ## Optional TLS Config for use on HTTP connections.
  # tls_ca = "/etc/telegraf/ca.pem"
  # tls_cert = "/etc/telegraf/cert.pem"
  # tls_key = "/etc/telegraf/key.pem"
  ## Use TLS but skip chain & host verification
  # insecure_skip_verify = false
```

[inputs] - A última e mais importante seção é a dos inputs. Os inputs são os plugins responsáveis pelas coletas dos dados e podem ser consultados nessa página: https://docs.influxdata.com/telegraf/v1.21/plugins/

Abaixo segue em código de exemplo/.

```bash
[[inputs.cpu]]
  ## Whether to report per-cpu stats or not
  percpu = true
  ## Whether to report total system cpu stats or not
  totalcpu = true
  ## If true, collect raw CPU time metrics
  collect_cpu_time = false
  ## If true, compute and report the sum of all non-idle CPU states
  report_active = false
[[inputs.disk]]
  ## By default stats will be gathered for all mount points.
  ## Set mount_points will restrict the stats to only the specified mount points.
  mount_points = ["/","/var","/var/log","/opt","/home","/tmp"]
  ## Ignore mount points by filesystem type.
  ignore_fs = ["tmpfs", "devtmpfs", "devfs", "iso9660", "overlay", "aufs", "squashfs"]
[[inputs.diskio]]
  ## By default, telegraf will gather stats for all devices including
  ## disk partitions.
  ## Setting devices will restrict the stats to the specified devices.
  # devices = ["sda", "sdb", "vd*"]
  ## Uncomment the following line if you need disk serial numbers.
  # skip_serial_number = false
  #
  ## On systems which support it, device metadata can be added in the form of
  ## tags.
  ## Currently only Linux is supported via udev properties. You can view
  ## available properties for a device by running:
  ## 'udevadm info -q property -n /dev/sda'
  ## Note: Most, but not all, udev properties can be accessed this way. Properties
  ## that are currently inaccessible include DEVTYPE, DEVNAME, and DEVPATH.
  # device_tags = ["ID_FS_TYPE", "ID_FS_USAGE"]
  #
  ## Using the same metadata source as device_tags, you can also customize the
  ## name of the device via templates.
  ## The 'name_templates' parameter is a list of templates to try and apply to
  ## the device. The template may contain variables in the form of '$PROPERTY' or
  ## '${PROPERTY}'. The first template which does not contain any variables not
  ## present for the device is used as the device name tag.
  ## The typical use case is for LVM volumes, to get the VG/LV name instead of
  ## the near-meaningless DM-0 name.
  # name_templates = ["$ID_FS_LABEL","$DM_VG_NAME/$DM_LV_NAME"]
[[inputs.mem]]
  # no configuration

[[inputs.netstat]]

[[inputs.net]]
  ## By default, telegraf gathers stats from any up interface (excluding loopback)
  ## Setting interfaces will tell it to gather these explicit interfaces,
  ## regardless of status.
  ##
  # interfaces = ["eth0"]
  ##
  ## On linux systems telegraf also collects protocol stats.
  ## Setting ignore_protocol_stats to true will skip reporting of protocol metrics.
  ##
  # ignore_protocol_stats = false
  ##
[[inputs.processes]]
  # no configuration
[[inputs.swap]]
  # no configuration
[[inputs.system]]
  ## Uncomment to remove deprecated metrics.
  # fielddrop = ["uptime_format"]
```

## Iniciando a coleta

Após a edição do arquivo de configuração, telegraf.conf, é preciso reinciar o serviço de telegraf para que as configurações sejam recarregadas indicando os novos parâmetros de coletas e envio dos dados. Para reiniciar o serviço deve-se executar o seguinte comando:

```bash
sudo systemctl restart telegraf
```

Após reiniciar o telegraf podemos visualizar o seu funcionamento através dos logs gerados com o comando:

```bash
tail -f /var/log/telegraf/telegraf.log
```

Se tudo estiver ocorrido sem erros iremos verificar uma saída semelhante a esta:

```bash
2022-04-25T09:23:11-03:00 I! Starting Telegraf 1.22.1
2022-04-25T09:23:11-03:00 I! Loaded inputs: cpu disk diskio mem net netstat processes swap system
2022-04-25T09:23:11-03:00 I! Loaded aggregators:
2022-04-25T09:23:11-03:00 I! Loaded processors:
2022-04-25T09:23:11-03:00 I! Loaded outputs: influxdb_v2
2022-04-25T09:23:11-03:00 I! Tags enabled: host=sgt-master
2022-04-25T09:23:11-03:00 I! [agent] Config: Interval:10s, Quiet:false, Hostname:"sgt-master", Flush Interval:10s
```

> Se na saída do log estiver acusando o seguinte erro:

```bash
2021-11-03T12:40:00Z E! [inputs.docker] Error in plugin: Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.21/info": dial unix /var/run/docker.sock: connect: permission denied,
2021-11-03T12:40:00Z E! [inputs.docker] Error in plugin: Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.21/containers/json?filters=%7B%22status%22%3A%5B%22running%22%5D%7D&limit=0": dial unix /var/run/docker.sock: connect: permission denied
```

É preciso adicionar o usuário do telegraf ao grupo do Docker com o comando:

```bash
sudo usermod -aG docker telegraf
```

## Tudo certo

Se todos os passos anteriores foram configurados com sucesso o Telegraf já estará enviando os dados coletados para o InfluxDB, basta acessar a UI do InfluxDB disponível em: https://watchdb.isitics.com/ e verificar os dados recebidos no bucket configurado.


## Modelo do Telegraf com Docker, PostgreSQL, Métricas do Host

```bash
# Configuration for telegraf agent
[agent]
  interval = "10s"
  round_interval = true
  metric_batch_size = 1000
  metric_buffer_limit = 10000
  collection_jitter = "0s"
  flush_interval = "10s"
  flush_jitter = "0s"
  precision = ""
  logfile = "/var/lib/docker/containers/*/*-json.log"
  log_with_timezone = "America/Recife"
  hostname = "sgt-production"
  omit_hostname = false

[[outputs.influxdb_v2]]
  urls = ["https://watchdb.isitics.com"]
  token = "O4wjF_IVqBBh8N6z-RyTsttQB3kL6xmnqwX4qdxw14VgWhcbIfz0g7960kym3xkuHcte_h8IvQVR9YNEMXnyZw==" # 
  organization = "isitics"
  bucket = "sgt"

[[inputs.cpu]]
  percpu = true
  totalcpu = true
  collect_cpu_time = false
  report_active = false
[[inputs.disk]]
  ignore_fs = ["tmpfs", "devtmpfs", "devfs", "iso9660", "overlay", "aufs", "squashfs"]
[[inputs.diskio]]
[[inputs.mem]]
[[inputs.net]]
[[inputs.netstat]]
[[inputs.processes]]
[[inputs.swap]]
[[inputs.system]]

#----------------DOCKER------------------------
[[inputs.docker]]
  endpoint = "unix:///var/run/docker.sock"

  gather_services = false
  container_name_include = []
  container_name_exclude = []
  timeout = "5s"
  docker_label_include = []
  docker_label_exclude = []
  perdevice = false
  perdevice_include = ["cpu", "blkio", "network"]
  total = true
  total_include = ["cpu", "blkio", "network"]


#---------------PostgreSQL---------------------
[[inputs.postgresql]]
  address ="host=rds-prod-sgt-database.cihplcbdebpy.sa-east-1.rds.amazonaws.com port=5432 user=postgres password=9OwWv0RQjCBl9vOiL9wlqbJghIpz5pQc database=sgt sslmode=disable"
```talação do Telegraf