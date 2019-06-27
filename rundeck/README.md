# Rundeck Helm Chart

Rundeck helm charts using the official rundeck docker image  

## Chart Details

This chart will do the following:

* Install a postgresql DB
* Install rundeck and expose the service on port 4440
* Install prometheus push notification plugin (https://github.com/marceloalmeida/prompush-notification)  
* Install Slack notification plugin (https://github.com/higanworks/rundeck-slack-incoming-webhook-plugin)  

## Installing the Chart
To install the chart with the release name `my-release` and the values file `my-values` in the namespace `my-namespace`.

You first need to create a secret for the postgresql database:

`kubectl create secret generic rundeck-postgresql --namespace my-namespace --from-file=./postgresql-password`  

where `postgresql-password` is a file containing a password

User authentication is done with two LDAP groups (admin and users) to plug rundeck instance with it a configmap is needed:

`kubectl create configmap rundeck-ldap --from-env-file=ldap.env --namespace my-namespace`

And then:

```bash
$ helm install -f rundeck/my-values.yaml --name my-release rundeck/ --namespace my-namespace
```

## Configuration

The following tables list the configurable parameters of the Rundeck chart and their default values.


| Parameter                         | Description                          | Default                                                                      |
| --------------------------------- | ------------------------------------ | ---------------------------------------------------------------------------- |
| `ldap.enabled`                    | Enable ldap plugin                   | `false`                                        |
| `ldap.configMap`                  | Existing configmap for ldap configuration                    | `rundeck-ldap`                                                            |
| `ldap.aclAdminGroup`                 | LDAP admin group to configure acls                   | `None`                                                                     |
| `ldap.aclUserGroup`          | LDAP user group (Run/Kill jobs)            | `None`                                                                     |
| `ldap.aclROGroup`          | LDAP read only group             | `None`     |
| `logPersistence.enabled`          | Enable log persistance for rundeck (in ceph)            | `false`   |
| `logPersistence.existingClaim`          | If a claim already existing             | `None`   |
| `logPersistence.size`          | Size of the ceph volume to create             | `10Gi`   |
| `logPersistence.annotations`          | Annotations for the PVC             | `None`   |
| `istio.enabled`          | Add a virtualservice file for istio            | `false`   |
| `istio.applicationDns`          | Host field of the istio virtualservice file     | `my-rundeck.mydomain.com`   |
| `istio.namespace`          | Namespace for the virtualservice      | `default`   |
| `istio.gateway`          | Gateway for the virtualservice     | `gateway`   |
| `istio.kubernetesClusterSuffix`          | Kubernetes Cluster suffix     | `cluster.local`   |
| `grailsUrl`          | Rundeck FQDN     | `https://my-rundeck.mydomain.com`   |
