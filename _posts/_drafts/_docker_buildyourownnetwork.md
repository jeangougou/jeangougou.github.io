Docker Network Layout !


Basic Isolated network

// lists all networks available to docker
docker network ls

// creates a new network (on top of your bridged virtual adapter)
docker network create --driver bridge my_network

// show the current status of the newly created network
docker network inspect my_network

// lists all networks available to docker
docker network ls

// add a busybox container to the network
docker run --net=my_network -itd --name=container3 busybox

// inspect the network again and notice the presence of the container specified
docker network inspect my_network


### I want to expose a port from my isolated network to be accessible to all other containers
list network configuration to map an internal container to connect to an external one.
Docker provides different network drivers to customize container networks. The drivers are _bridge_ or _overlay_, but it is also possible to customize it even further by adding _network plugin_ or _remote network_. <You can create multiple networks. You can add containers to more than one network. Containers can only communicate within networks but not across networks. A container attached to two networks can communicate with member containers in either network. When a container is connected to multiple networks, its external connectivity is provided via the first non-internal network, in lexical order.>


## then create 2 siblings network and connect them to each other or through an intermediary

214  2016.06.03 19:22:46 docker network ls
 215  2016.06.03 19:23:12 docker network create --driver bridge net1
 216  2016.06.03 19:23:55 docker network create --driver overlay neto2
 217  2016.06.03 19:24:09 docker network create --driver bridge net2
 218  2016.06.03 19:24:26 docker run -itd --net=net1 busibox
 219  2016.06.03 19:24:52 docker run --net=net1 -itd --name=c3 busybox
 220  2016.06.03 19:25:01 docker run --net=net2 -itd --name=c2 busybox
 221  2016.06.03 19:27:32 docker network connect net1 c2
 222  2016.06.03 19:27:40 docker network inspect net1
 223  2016.06.03 19:27:55 docker network inspect net2
 224  2016.06.03 19:28:02 docker network inspect net1
 225  2016.06.03 19:28:23 docker exec c2 ifconfig

// follow ups:
https://docs.docker.com/engine/userguide/networking/work-with-networks/#linking-containers-in-user-defined-networks
https://github.com/wsargent/docker-cheat-sheet


## what they don't tell you
 The overlay network requires a valid key-value store service and each host in the network must run a Docker Engine instance.
 Docker has libraries supporting Consul, Etcd, and ZooKeeper. Alternatively Mesos, Swarm or Kubernetes have to come into play for this level of orchestration.
