Setting up & using the CNI port mapper
======================================

On the Mesos agent include the following configuration for Mesos:
```
MESOS_NETWORK_CNI_CONFIG_DIR=/etc/cni/net.d
MESOS_NETWORK_CNI_PLUGINS_DIR=/opt/cni/bin
```
In `/etc/cni/net.d/` put two files: `10-bridge.conf` containing:
```
{
   "name":"cni-bridge",
   "type":"bridge",
   "bridge":"mesos-cni0",
   "isGateway":true,
   "ipMasq":true,
   "ipam":{
      "type":"host-local",
      "subnet":"192.168.0.0/16",
      "routes":[
         {
            "dst":"0.0.0.0/0"
         }
      ]
   }
}
```
and `20-port-mapper.conf` containing:
```
{
   "name":"mesos-port-mapper",
   "type":"mesos-cni-port-mapper",
   "excludeDevices":[
      "mesos-cni0"
   ],
   "chain":"MESOS-PORT-MAPPER",
   "delegate":{
      "type":"bridge",
      "bridge":"mesos-cni0",
      "isGateway":true,
      "ipMasq":true,
      "ipam":{
         "type":"host-local",
         "subnet":"192.168.0.0/16",
         "routes":[
            {
               "dst":"0.0.0.0/0"
            }
         ]
      }
   }
}
```
Copy the CNI plugins from https://github.com/containernetworking/plugins/releases into `/opt/cni/bin` as well as the port mapper plugin:
```
ln -s /usr/libexec/mesos/mesos-cni-port-mapper /opt/cni/bin/mesos-cni-port-mapper

```
Example TaskInfo whicb runs nginx and maps port 80 in the container to port 31000 on the host:
```
{
   "name":"nginx",
   "task_id":{
      "value":"nginx"
   },
   "agent_id":{
      "value":""
   },
   "resources":[
      {
         "name":"cpus",
         "type":"SCALAR",
         "scalar":{
            "value":0.1
         }
      },
      {
         "name":"mem",
         "type":"SCALAR",
         "scalar":{
            "value":32
         }
      }
   ],
   "command":{
      "value":"nginx -g 'daemon off;'"
   },
   "container":{
      "type":"MESOS",
      "mesos":{
         "image":{
            "type":"DOCKER",
            "docker":{
               "name":"nginx"
            }
         }
      },
      "network_infos":[
         {
            "name":"mesos-port-mapper",
            "port_mappings":[
               {
                  "host_port":31000,
                  "container_port":80,
                  "protocol":"tcp"
               }
            ]
         }
      ]
   }
}
```

