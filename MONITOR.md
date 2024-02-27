# Container Monitor

Container [monitoring script](templates/monitor.sh.j2) is present as a service to update Consul metadata with current state of a service.

It listens to Docker events for given container and updates the JSON service definition in `/etc/consul`.
```
jakubgs@node-01.do-ams3.wakuv2.test:~ % systemctl -o cat -n5 status monitor-nim-waku-v2 | cat
● monitor-nim-waku-v2.service - Container state monitor for nim-waku-v2)
     Loaded: loaded (/etc/systemd/system/monitor-nim-waku-v2.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2024-02-27 22:42:52 UTC; 13h ago
       Docs: https://github.com/status-im/infra-role-nim-waku
   Main PID: 2350961 (bash)
      Tasks: 7 (limit: 2234)
     Memory: 7.9M
        CPU: 1.923s
     CGroup: /system.slice/monitor-nim-waku-v2.service
             ├─2350961 bash /docker/nim-waku-v2/monitor.sh
             ├─2350962 docker system events "--format={{ json . }}" --filter=container=nim-waku-v2 --filter=event=stop --filter=event=start --filter=event=restart --filter=event=die --filter=event=kill
             └─2350963 bash /docker/nim-waku-v2/monitor.sh

{"time":1709073779,"commit":"9a2f4e95","Action":"kill"}
Changes detected, reloading Consul.
{"time":1709073780,"commit":"9a2f4e95","Action":"stop"}
{"time":1709073780,"commit":"9a2f4e95","Action":"die"}
```

# Known Issues

The method of modifying JSON files in `/etc/consul` using `sed` is crude but simple. It avoids the need to have to call [Consul Catalog API](https://developer.hashicorp.com/consul/api-docs/catalog#register-entity) with the whole set of service metadata only to update a single `ServiceMeta` key.

But there are a few problems with this approach:

* It only works for metadata fields that are already present in the JSON file.
* It causes Ansible to overwrite the fields, setting them to `unknown`.
* It can fail due to JSON file format changes.
