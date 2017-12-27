# redis-rdb-persistence

## usage

redis コンテナを立ち上げて、key/valueをSETしてみる.

```
your-local-machine$ docker-compose up -d
Creating network "redispersistency_default" with the default driver
Creating redispersistency_redis_1 ...
Creating redispersistency_redis_1 ... done

your-local-machine$ docker exec -it redispersistency_redis_1 sh

/data # ps aux
PID   USER     TIME   COMMAND
    1 redis      0:00 redis-server
   14 root       0:00 sh
   19 root       0:00 ps aux

/data # redis-cli

127.0.0.1:6379> keys *
(empty list or set)
127.0.0.1:6379> set foo bar
OK
127.0.0.1:6379> set hoge fuga
OK
127.0.0.1:6379> keys *
1) "hoge"
2) "foo"
127.0.0.1:6379> exit

/data # exit
```

コンテナを落としてみよう. 普通ならこれでデータは全て消える.

```
your-local-machine$ docker-compose down
Stopping redispersistency_redis_1 ... done
Removing redispersistency_redis_1 ... done
Removing network redispersistency_default
```

再度コンテナを立ち上げ直してみる. データがちゃんと永続化されている.

```
your-local-machine$ docker-compose up -d
Creating network "redispersistency_default" with the default driver
Creating redispersistency_redis_1 ...
Creating redispersistency_redis_1 ... done

your-local-machine$ docker exec -it redispersistency_redis_1 sh

/data # ps aux
PID   USER     TIME   COMMAND
    1 redis      0:00 redis-server
   10 root       0:00 sh
   14 root       0:00 ps aux

/data # redis-cli

127.0.0.1:6379> keys *
1) "hoge"
2) "foo"
```

## rdb settings

Image ``redis:3.2-alpine`` 標準の RDB Snapshot 設定はこうなっている.

```
save 3600 1
save 300 100
save 60 10000
```

RDBファイルの保存先は↓.

```
/data/dump.rdb
```

設定は↓で見れる.

```
# redis-cli
127.0.0.1:6379> config get *
  1) "dbfilename"
  2) "dump.rdb"
  3) "requirepass"
  4) ""
  5) "masterauth"
  6) ""
  7) "unixsocket"
  8) ""
  9) "logfile"
 10) ""
 11) "pidfile"
 12) ""
 13) "slave-announce-ip"
 14) ""
 15) "maxmemory"
 16) "0"
 17) "maxmemory-samples"
 18) "5"
 19) "timeout"
 20) "0"
 21) "auto-aof-rewrite-percentage"
 22) "100"
 23) "auto-aof-rewrite-min-size"
 24) "67108864"
 25) "hash-max-ziplist-entries"
 26) "512"
 27) "hash-max-ziplist-value"
 28) "64"
 29) "list-max-ziplist-size"
 30) "-2"
 31) "list-compress-depth"
 32) "0"
 33) "set-max-intset-entries"
 34) "512"
 35) "zset-max-ziplist-entries"
 36) "128"
 37) "zset-max-ziplist-value"
 38) "64"
 39) "hll-sparse-max-bytes"
 40) "3000"
 41) "lua-time-limit"
 42) "5000"
 43) "slowlog-log-slower-than"
 44) "10000"
 45) "latency-monitor-threshold"
 46) "0"
 47) "slowlog-max-len"
 48) "128"
 49) "port"
 50) "6379"
 51) "tcp-backlog"
 52) "511"
 53) "databases"
 54) "16"
 55) "repl-ping-slave-period"
 56) "10"
 57) "repl-timeout"
 58) "60"
 59) "repl-backlog-size"
 60) "1048576"
 61) "repl-backlog-ttl"
 62) "3600"
 63) "maxclients"
 64) "10000"
 65) "watchdog-period"
 66) "0"
 67) "slave-priority"
 68) "100"
 69) "slave-announce-port"
 70) "0"
 71) "min-slaves-to-write"
 72) "0"
 73) "min-slaves-max-lag"
 74) "10"
 75) "hz"
 76) "10"
 77) "cluster-node-timeout"
 78) "15000"
 79) "cluster-migration-barrier"
 80) "1"
 81) "cluster-slave-validity-factor"
 82) "10"
 83) "repl-diskless-sync-delay"
 84) "5"
 85) "tcp-keepalive"
 86) "300"
 87) "cluster-require-full-coverage"
 88) "yes"
 89) "no-appendfsync-on-rewrite"
 90) "no"
 91) "slave-serve-stale-data"
 92) "yes"
 93) "slave-read-only"
 94) "yes"
 95) "stop-writes-on-bgsave-error"
 96) "yes"
 97) "daemonize"
 98) "no"
 99) "rdbcompression"
100) "yes"
101) "rdbchecksum"
102) "yes"
103) "activerehashing"
104) "yes"
105) "protected-mode"
106) "no"
107) "repl-disable-tcp-nodelay"
108) "no"
109) "repl-diskless-sync"
110) "no"
111) "aof-rewrite-incremental-fsync"
112) "yes"
113) "aof-load-truncated"
114) "yes"
115) "maxmemory-policy"
116) "noeviction"
117) "loglevel"
118) "notice"
119) "supervised"
120) "no"
121) "appendfsync"
122) "everysec"
123) "syslog-facility"
124) "local0"
125) "appendonly"
126) "no"
127) "dir"
128) "/data"
129) "save"
130) "3600 1 300 100 60 10000"
131) "client-output-buffer-limit"
132) "normal 0 0 0 slave 268435456 67108864 60 pubsub 33554432 8388608 60"
133) "unixsocketperm"
134) "0"
135) "slaveof"
136) ""
137) "notify-keyspace-events"
138) ""
139) "bind"
140) ""
```

## references

* https://hub.docker.com/_/redis/
* https://redis.io/topics/persistence
