# docker-dnsmasq

It's a [dnsmasq][dnsmasq] Docker image. It is only 6 MB in size. It is just an `ENTRYPOINT` to the `dnsmasq` binary. Can you smell what the rock is cookin'?

## Usage

It is usually a good idea to use a tag other than `latest` if you are using this image in a production setting. There are several tags to choose from:

* `andyshinn/dnsmasq:2.72`: dnsmasq 2.72 based on Alpine 3.2
* `andyshinn/dnsmasq:2.75`: dnsmasq 2.75 based on Alpine 3.3
* `andyshinn/dnsmasq:2.76`: dnsmasq 2.76 based on Alpine 3.4
* `andyshinn/dnsmasq:2.78`: dnsmasq 2.78 based on Alpine Edge
* `andyshinn/dnsmasq:2.79`: dnsmasq 2.79 based on Alpine 3.8
* `andyshinn/dnsmasq:2.80`: dnsmasq 2.80 based on Alpine 3.11
* `andyshinn/dnsmasq:2.81`: dnsmasq 2.81 based on Alpine 3.12

[dnsmasq][dnsmasq] requires `NET_ADMIN` capabilities to run correctly. Start it with something like `docker run -p 53:53/tcp -p 53:53/udp --cap-add=NET_ADMIN andyshinn/dnsmasq:2.75`.

The configuration is all handled on the command line (no wrapper scripts here). The `ENTRYPOINT` is `dnsmasq -k` to keep it running in the foreground. If you wanted to send requests for an internal domain (such as Consul) you can forward the requests upstream using something like `docker run -p 53:53/tcp -p 53:53/udp --cap-add=NET_ADMIN andyshinn/dnsmasq:2.75 -S /consul/10.17.0.2`. This will send a request for `redis.service.consul` to `10.17.0.2`

As this is a very barebones entrypoint with just enough to run in the foreground, there is no logging enabled by default. To send logging to stdout you can add `--log-facility=-` as an option.

[dnsmasq]: http://www.thekelleys.org.uk/dnsmasq/doc.html

## Dnsmasq工作原理

当接受到一个DNS请求时，Dnsmasq首先会查找/etc/hosts这个文件，然后查找/etc/resolv.conf中定义的外部DNS。Dnsmasq是一个很好的外部DNS中继。
配置Dnsmasq为DNS缓存服务器，同时在/etc/hosts文件中加入本地内网解析，这样一来每当内网机器查询时就会优先查询hosts文件，这就等于将/etc/hosts共享给全内网机器使用，从而解决内网机器互相识别的问题。
