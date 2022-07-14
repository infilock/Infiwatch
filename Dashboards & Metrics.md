# InfiWatch's Dashboards & Metrics

Reference page for InfiWatch's dashboards & metrics.

## Fabric Infrastructure Monitoring Dashboard

![infiwatch](./images/Fabric%20Infrastructure%20Monitoring%20Dashboard%20-%20InfiWatch.png)

- **Control Panel (Nodes):** Select your target node so you can only see information for that node.
- **Blockchain Version:** Installed Hyperldger Fabric's version
- **Active Peers:** Total active peers count
- **Active Orderers:** Total active orderers count
- **Go Version:** Installed Go's version
- **Log Volume:** Log volume timeline divided by orderers and peers
- **Log Distribution:** Log distribution between all active orderers and peers
- **Memory Usage:** HyperLedger Fabric's memory usage timeline
- **Open File Descriptors:** Total open file descriptors over time
- **gRPC Active Connections:** Total gRPC active connections timeline. gRPC is an RPC framework used by Hyperledger Fabric for internal communication between nodes.

## Fabric Transaction Monitoring Dashboard

![infiwatch](./images/Fabric%20Infrastructure%20Monitoring%20Dashboard%20-%20InfiWatch.png)

- **Control Panel (Channels & Chaincodes):** Select your target channel or chain code so you can only see information for that channel/node.
- **Transaction Rate:** Compare transaction rate to the last time fragmentation 
- **Blockchain Height:** Total Blockchain height from the beginning
- **Total Transaction:** Total transaction made from the beginning
- **Transaction History:** Transaction made's history timeline
- **Cumulative Transaction Count:** Total transaction count over time. It only adds up.
- **Transaction DrillDown:** Transaction drilled down by nodes and statuses
- **Transaction Count:** Compare transaction count between different chain codes.
- **Endorser Proposal Failures:** Proposal failures history has shown with the help of a timeline
- **Transaction Validation Duration:** This displays how long it will take for your Hyperledger Fabric's instance to validate a transaction.
- **Proposal Duration:** This shows how long it will take to send a proposal and get a response.

## What to Watch and What to Care

Always be aware of *significant changes in patterns*. For example, *dramatically increase* in transaction rate probably means somebody is trying to DoS you, or a *decrease* probably implies somebody or something went offline. 

Be aware of *total active nodes and peers*. Always investigate any changes.

*Changes in versions* are profound too. Always make sure that you were the person who changed the software version, not anybody or any system else. These changes are dangerous in both security and non-security (software compatibility) ways.

## InfiWatch's Privacy Details

InfiWatch will not collect sensitive or critical information like username and passwords, tokens, or transaction content. It only contains metrics from exposed `Prometheus` instances by HyperLedger Fabric's nodes.

### Metrics list

Collected metrics are:

- ledger_transaction_count
- ledger_blockchain_height
- process*
- fabric_version
- endorser_proposals_received
- endorser_successful_proposals
- go_info
- broadcast_validate_duration
- endorser_proposal_duration
- grpc_comm_conn_closed
- grpc_comm_conn_opened
- endorser_endorsement_failures
- broadcast_processed_count

For more details, please visit Hyperledger Fabric's official documentation:

- [Metrics Reference](https://hyperledger-fabric.readthedocs.io/en/release-2.2/metrics_reference.html) by Hyperledger Fabric