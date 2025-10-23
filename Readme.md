create a bash script to automatically get the running cluster 
```bash
nano ~/.ssh/emr-host.sh
```

Paste following content : 


```bash
#!/usr/bin/env bash
# ~/.ssh/emr-host.sh
# Prints the MasterPublicDnsName of the latest active EMR cluster

CLUSTER_ID=$(aws emr list-clusters --active --query 'Clusters[0].Id' --output text 2>/dev/null)

if [ -z "$CLUSTER_ID" ] || [ "$CLUSTER_ID" = "None" ]; then
    echo "No active EMR cluster found" >&2
    exit 1
fi

aws emr describe-cluster --cluster-id "$CLUSTER_ID" \
    --query 'Cluster.MasterPublicDnsName' \
    --output text
```

Make it executable : 


```bash
chmod +x ~/.ssh/emr-host.sh
```

Now add it to ssh config 

```bash
nano ~/.ssh/config
```


Paste following content : 

remember : replace user and identity file : this should point to your ssh file and username on the server


```bash
Host ubsemr
    User raj
    IdentityFile ~/.ssh/id_rsa
    StrictHostKeyChecking no
    UserKnownHostsFile=/dev/null
    ProxyCommand bash -c "nc $(~/.ssh/emr-host.sh) 22"
    LocalForward 8888 localhost:8888 # we will use this for the jupyterlab
```

Now do ssh to system 

``` bash 
ssh ubsemr 
``` 

now inside server install jupyterlab 

```bash
pip install jupyterlab 
```

now launch it 

```bash 
jupyter lab
```

make sure you are not running jupyterlab locally as it will create confict for your tunnelling 

now open the jupyter lab link locally 


you can also get your code from the git directly if you like in the server 

```bash
git clone https://github.com/kshitijrajsharma/fraction-ml-distributed-computing.git
```