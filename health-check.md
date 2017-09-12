Simple example of a task with a HTTP health check:
````
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
      }
   },
   "health_check":{
      "type":"HTTP",
      "http":{
         "scheme":"http",
         "port":80
      },
      "delay_seconds":5,
      "interval_seconds":4,
      "timeout_seconds":1
   }
}
````