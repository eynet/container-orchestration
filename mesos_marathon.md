## Architecture 

![](http://mesos.apache.org/assets/img/documentation/architecture3.jpg)

### Components
- Mesos Master
- Mesos Agent Nodes
- Zookeeper 
- Frameworks
 - Marathon
 - Chronos
 - ...

### Defining an application 
- Individual app
- Application Group

#### RSVP App
##### Database
```
{
  "id": "mongodb",
  "cmd": null,
  "cpus": 1,
  "mem": 128,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "mongo:3.3",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 27017,
          "protocol": "tcp",
          "servicePort": 27017,
          "name": "mongo"
        }
      ],
      "parameters": []
    },
    "type": "DOCKER",
    "volumes": []
  },
  "env": {
    "MONGODB_DATABASE": "rsvpdata"
  },
  "labels": {},
  "healthChecks": []
}
```

##### RSVP
```
{
  "id": "rsvp",
  "cmd": null,
  "cpus": 1,
  "mem": 128,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "teamcloudyuga/rsvpapp",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 5000,
          "protocol": "tcp",
          "name": "rsvp"
        }
      ]
    },
    "type": "DOCKER"
  },
  "env": {
    "MONGODB_HOST": "mongodb.marathon.mesos"
  }
}
```

#### High availablity of application 
- Application scales on multiple slave nodes 

#### Service discovery and Load Balancing an application
- [Mesos-DNS](https://github.com/mesosphere/mesos-dns) - provides service discovery through the domain name system (DNS).
- [Marathon-lb](https://github.com/mesosphere/marathon-lb) - provides port-based service discovery using HAProxy.    

#### Autoscaling an application 
- Requests Per Second (RPS) based [marathon based autoscale](https://github.com/mesosphere/marathon-lb-autoscale)
- [CPU and Memory based auto-scaling](https://docs.mesosphere.com/1.7/usage/tutorials/autoscaling/cpu-memory/) 

#### Rolling upgrade and rollback of an application 
- [Rolling Restarts](https://mesosphere.github.io/marathon/docs/deployments.html#rolling-restarts) with **minimumHealthCapacity** option.

#### Internally connecting to other application 
#### Networking option to connect applications with-in the cluster  
- Marathon's Docker integration maps conatiner's port to hpst port 
- Container does not get invidual IP address. 

But with DC/OS 1.8 with Marathon 1.3 
- Service are built-in with Marathon. The native DC/OS Marathon instance UI is now fully integrated with the DC/OS UI.
- Each container gets an IP address with VxLAN based virual network.
- DNS Based Service Addresses for Load Balanced Virtual IPs

#### Accessing the application from external world 
- Using Load Balancer like HAProxy 

#### Managing storage for application
- Persistent Local volume
- External Persistent volumes
 - Amazon EBS
 - [Experimental Flocker Plugin](https://docs.clusterhq.com/en/latest/mesos-integration/index.html) 


## Demo 
### Deploy Mesos 
```
$ cd container-orchestration/mesos-marathon
$ export DO_TOKEN=adfvvfvs............
$ vagrant up --provider=digital_ocean
$ vagrant ssh
$ wget https://raw.githubusercontent.com/cloudyuga/container-orchestration/master/mesos-marathon/deploy.sh
$ https://raw.githubusercontent.com/cloudyuga/container-orchestration/master/mesos-marathon/docker-compose.yml
$ sh delpoy.sh
```

### Deploy the application 
#### Open the Marathon GUI from your workstation
```
$ open http://$HOST_IP:8080 
```

#### Deploy *mongodb* and *rsvp* containers using following json files
- [mongodb](https://raw.githubusercontent.com/cloudyuga/container-orchestration/master/mesos-marathon/monogo.json)
- [RSVP Web app](https://raw.githubusercontent.com/cloudyuga/container-orchestration/master/mesos-marathon/rsvp.json)
 

### Destroy the setup
```
$ vagrant destory -f
```
