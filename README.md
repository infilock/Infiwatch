# InfiWatch

InfiWatch is a free-to-use platform-as-a-service (PaaS) that makes it easy to ingest, search, visualize, and analyze your Hyperledger Fabric's data. This is the Infilock Enterprise analysis platform in the form of PaaS that you can start using in a matter of seconds, with no prior technical expertise required!

We built InfiWatch on top of OpenSearch. OpenSearch is a distributed, open-source search and analytics suite used for a broad set of use cases like real-time application monitoring, log analytics, and website search. OpenSearch provides a highly scalable system for providing fast access and response to large volumes of data with an integrated visualization tool, OpenSearch Dashboards, that makes it easy for users to explore their data. 

[![infiwatch](https://user-images.githubusercontent.com/108833121/177629891-52565ce6-b85c-4388-a581-32ad43c59ef5.png)](https://www.youtube.com/watch?v=zkk19RiKsJk)

# Setup Instruction for pure Hyperledger Fabric

## Get Credentials

To use InfiWatch, users must register and get their own set of credentials. To do that, users need to contact `info@infilock.io` and ask for registration. Infilock's support team will gladly hand you the required credentials within hours. 

The credentials' package contains:
- client.crt: Your client's public certificate.
- client.key: Your client's private certificate.
- ca.crt: Our certificate authority's certificate.
- Instance id: Your special Instance ID to access the data of yours.
- metricbeat.yml: Example integration configuration file.
- username and password

*Be careful: Do not share these credentials with anybody.*

## Integrate to InfiWatch

Download and extract the credentials:

``` sh
$ pwd
/home/myuser

$ unzip infiwatch_credentails.zip

$ cd infiwatch_credentails

$ pwd
/home/myuser/infiwatch_credentails
```

Given metricbeat example (`metricbeat.yml`) looks like this:

``` yml
metricbeat.modules:
  - module: prometheus
    period: 10s
    enabled: true
    hosts: # Change me
      - orderer0.hyperledger.network.local:8443
      - orderer1.hyperledger.network.local:8443
      - orderer2.hyperledger.network.local:8443
      - peer0.hyperledger.network.local:9443
      - peer1.hyperledger.network.local:9443
    metrics_path: /metrics
    metricsets: ["collector"]
    metrics_filters:
      include: ["^ledger_transaction_count$", "^ledger_blockchain_height$", "^process*", "^fabric_version$", "^endorser_proposals_received$", "^endorser_successful_proposals$", "^go_info$", "^broadcast_validate_duration$", "^endorser_proposal_duration$", "^grpc_comm_conn_closed$", "^grpc_comm_conn_opened$", "^endorser_endorsement_failures$", "^broadcast_processed_count$"]

  output.logstash:
    hosts: ["telemetry.infilock.io:5044"]
    ssl.certificate_authorities: ["/etc/ca.crt"]
    ssl.certificate: "/etc/client.crt"
    ssl.key: "/etc/client.key"

  processors:
  - add_fields:
      target: meta
      fields:
        instanceid: 3a7d7c18-fg62-49aa-ac89-ff11426f60d3 # Change me
```

To use this example, you need to customize it according to your setup. First things first, the `hosts` section needs to be edited. To do that, you need to add your Hyperledger nodes' IPs.

To find required IPs you can use the following helper command:

```
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container_name_or_id
```

Change the `container_name_or_id` and put the target node's name or id. Repeat this process to find all nodes' IPs.

Edit the `instanceid` and put the unique identifier handed to you via email. 

Finally, to run the metricbeat, first, make sure you are in the `/path/to/your/infiwatch_credentails` path, and then execute this command:

``` sh
docker run -d \
    --name=metricbeat-infiwatch \
    --user=root \
    --network="myhyperledgernetwork" \
    --volume="$(pwd)/metricbeat.yml:/usr/share/metricbeat/metricbeat.yml:ro" \
    --volume="/var/run/docker.sock:/var/run/docker.sock:ro" \
    --volume="/sys/fs/cgroup:/hostfs/sys/fs/cgroup:ro" \
    --volume="/proc:/hostfs/proc:ro" \
    --volume="/:/hostfs:ro" \
    --volume="$(pwd)/keys/ca.crt:/etc/ca.crt" \
    --volume="$(pwd)/keys/client.crt:/etc/client.crt" \
    --volume="$(pwd)/keys/client.key:/etc/client.key" \
    docker.elastic.co/beats/metricbeat-oss:8.2.3 metricbeat
```

You need to edit `--network="myhyperledgernetwork"` according to your Hyperledger Fabric's network name. To find the corresponding network name, you can execute the following command:

``` sh
$ docker network ls

NETWORK ID     NAME                   DRIVER    SCOPE
91d25377c93c   bridge                 bridge    local
0804a9a67e4c   host                   host      local
193aca780ffa   myhyperledgernetwork   bridge    local
f327e7ad9796   none                   null      local
```

## Watch data in InfiWatch
Login to [InfiWatch](http://watch.infilock.io/app/login? "InfiWatch's Panel") by the username and password and watch your data in pre-defined dashboards.

# Setup on Hyperledger Fabric deployed on Kubernetes
## Get Credentials

To use InfiWatch, users must register and get their own set of credentials. To do that, users need to contact `info@infilock.io` and ask for registration. Infilock's support team will gladly hand you the required credentials within hours. 

The credentials' package contains:
- client.crt: Your client's public certificate.
- client.key: Your client's private certificate.
- ca.crt: Our certificate authority's certificate.
- Instance id: Your special Instance ID to access the data of yours.
- metricbeat.yml: Example integration configuration file.
- username and password.

*Be careful: Do not share these credentials with anybody.*

Download and extract the credentials.

Given metricbeat example (`metricbeat.yml`) looks like this:

``` yml
daemonset:
  enabled: false
deployment:
  metricbeatConfig:
    metricbeat.yml: |
      metricbeat.modules:
        - module: prometheus
          period: 10s
          enabled: true
          hosts:
            - cbiot-orderer0.cbiot.svc.cluster.local:8443
            - cbiot-orderer1.cbiot.svc.cluster.local:8443
            - cbiot-orderer2.cbiot.svc.cluster.local:8443
            - cbiot-peer0.cbiot.svc.cluster.local:9443
            - cbiot-peer1.cbiot.svc.cluster.local:9443
          metrics_path: /metrics
          metricsets: ["collector"]
          metrics_filters:
            include: ["^ledger_transaction_count$", "^ledger_blockchain_height$", "^process*", "^fabric_version$", "^endorser_proposals_received$", "^endorser_successful_proposals$", "^go_info$", "^broadcast_validate_duration$", "^endorser_proposal_duration$", "^grpc_comm_conn_closed$", "^grpc_comm_conn_opened$", "^endorser_endorsement_failures$", "^broadcast_processed_count$"]
      output.file:
        enabled: false
      output.logstash:
        enabled: true
        hosts: ["telemetry.infilock.io:5044"]
        ssl.certificate_authorities: ["/usr/share/metricbeat/ca/certs/tls.crt"]
        ssl.certificate: "/usr/share/metricbeat/config/certs/tls.crt"
        ssl.key: "/usr/share/metricbeat/config/certs/tls.key"
      processors:
      - add_fields:
          target: meta
          fields:
            instanceid: 3a7d7c17-de62-49aa-ac79-ee12026f60d3
  secretMounts:
    - name: metricbeat-certificate-pem
      secretName: metricbeat-certificate
      path: /usr/share/metricbeat/config/certs
    - name: metricbeat-ca-pem
      secretName: metricbeat-ca
      path: /usr/share/metricbeat/ca/certs
imageTag: 7.10.2
image: docker.elastic.co/beats/metricbeat-oss
```

## Integrate to InfiWatch
### Pre-requisits:
1- Hyperledger Fabric is installed on Kubernetes
2- Kubectl and Helm are installed

### Step1: Setup
### Create a namespace (e.g., monitoring)
```shell
kubectl create namespace <monitoring>
```

### Step2: Install

1 - import client cert secrets
```shell
kubectl create secret tls metricbeat-certificate -n <monitoring> --key="client.key" --cert="client.crt"
```

2 - import ca.crt secret : create a TLS secret in the namespace(e.g., monitoring) manually either by Rancher or Kubectl. Replace ca.crt and leave Private Key blank.

3 - install metricbeat
Put the metricbeat.yaml file in a folder in local system and run the following commands.
```shell
helm repo add stable https://charts.helm.sh/stable
helm repo add elastic https://helm.elastic.co

helm install --namespace <monitoring> -f <path/to/metricbeat.yaml> path metricbeat elastic/metricbeat
```

## Step3: Upgrade

```shell
helm upgrade -n monitoring -f <path/to/metricbeat.yaml> metricbeat elastic/metricbeat
```

## Watch data in InfiWatch
Login to [InfiWatch](http://watch.infilock.io/app/login? "InfiWatch's Panel") by the username and password and watch your data in pre-defined dashboards.
