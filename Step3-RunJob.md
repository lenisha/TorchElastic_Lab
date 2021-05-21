# Trianing Job

## Docker Prepare Image ? (non needed if MCR)

## Kubernetes Job config

- Update `imagenet.yaml` with `tolerations` and `nodeSelector` to run training on Spot VM nodepool

```yaml
    nodeSelector:
       kubernetes.azure.com/scalesetpriority: spot
    tolerations:
    - key: "kubernetes.azure.com/scalesetpriority"
      operator: "Equal"
      value: "spot"
      effect: "NoSchedule"       
```

- Update `imagenet.yaml` with `voumes` and `volumemount` to provide storage to the training job to save checkpoint and load data 

```yaml
 volumes:  
 - name: trainingdata
   persistentVolumeClaim:
      claimName: pvc-blob
 containers:
  ...
   - "--workers=0"
   - "/workspace/data/tiny-imagenet-200"
   - "--checkpoint-file=/mnt/blob/data/checkpoint.pth.tar"
  volumeMounts:
  - name: trainingdata
    mountPath: "/mnt/blob/data"         
```


## Run and monitor the job

- Run the job
```
kubectl apply -f kube/imagenet.yaml
```

- Verify the job 

```sh
kubectl describe elasticjob
```

You would see similar output
```
Status:
  Conditions:
    Last Transition Time:  2021-05-21T22:50:50Z
    Last Update Time:      2021-05-21T22:50:50Z
    Message:               ElasticJob imagenet is running.
    Reason:                ElasticJobRunning
    Status:                True
    Type:                  Running
  Replica Statuses:
    Worker:
      Active:  2
Events:
  Type    Reason                   Age   From                    Message
  ----    ------                   ----  ----                    -------
  Normal  SuccessfulCreatePod      17m   elastic-job-controller  Created pod: imagenet-worker-0
  Normal  SuccessfulCreatePod      17m   elastic-job-controller  Created pod: imagenet-worker-1
  Normal  SuccessfulCreateService  17m   elastic-job-controller  Created service: imagenet-worker-0
  Normal  SuccessfulCreateService  17m   elastic-job-controller  Created service: imagenet-worker-1
```

and 
```
kubectl get pods

NAME                                          READY   STATUS    RESTARTS   AGE
elastic-job-k8s-controller-5b9bc6b79c-4z7mn   1/1     Running   0          8h
etcd                                          1/1     Running   0          8h
imagenet-worker-0                             1/1     Running   0          18m
imagenet-worker-1                             1/1     Running   0          18m
```

- Verify Logs of the workers - check `group_rank` - each worker will report unique rank number

```sh
kubectl logs imagenet-worker-0 -f
```

```
INFO] 2021-05-21 22:53:19,192 launch: Running torchelastic.distributed.launch with args: ['/opt/conda/lib/python3.7/site-packages/torchelastic/distributed/launch.py', '--rdzv_backend=etcd', '--rdzv_endpoint=etcd-service:2379', '--rdzv_id=imagenet', '--nnodes=1:2', '--nproc_per_node=1', '/workspace/examples/imagenet/main.py', '--arch=resnet18', '--epochs=3', '--batch-size=64', '--workers=0', '/workspace/data/tiny-imagenet-200', '--checkpoint-file=/mnt/blob/data/checkpoint.pth.tar']
INFO 2021-05-21 22:53:19,201 Etcd machines: ['http://0.0.0.0:2379']
...

Result:
        restart_count=0
        group_rank=1
        group_world_size=2
        rank stride=1
        assigned global_ranks=[1]
        master_addr=imagenet-worker-0
        master_port=48109

[INFO] 2021-05-21 22:53:34,460 api: [default] Starting worker group
=> set cuda device = 0
=> creating model: resnet18
=> no workers have checkpoints, starting from epoch 0
=> start_epoch: 0, best_acc1: 0
Epoch: [0][  0/782]     Time  3.479 ( 3.479)    Data  0.125 ( 0.125)    Loss 7.0334e+00 (7.0334e+00)    Acc@1   0.00 (  0.00)   Acc@5   0.00 (  0.00)
Epoch: [0][ 10/782]     Time  0.794 ( 1.044)    Data  0.097 ( 0.104)    Loss 6.0205e+00 (6.3747e+00)    Acc@1   0.00 (  0.43)   Acc@5   0.00 (  1.85)
Epoch: [0][ 20/782]     Time  0.779 ( 0.930)    Data  0.093 ( 0.103)    Loss 5.6551e+00 (6.0445e+00)    Acc@1   3.12 (  1.34)   Acc@5   7.81 (  4.54)
Epoch: [0][ 30/782]     Time  0.789 ( 0.892)    Data  0.091 ( 0.104)    Loss 5.3774e+00 (5.9030e+00)    Acc@1   1.56 (  1.36)   Acc@5   3.12 (  4.59)
Test: [130/157] Time  0.205 ( 0.218)    Loss 7.3066e+00 (7.1833e+00)    Acc@1   1.56 (  1.12)   Acc@5   4.69 (  4.02)
Test: [140/157] Time  0.223 ( 0.218)    Loss 7.0710e+00 (7.1881e+00)    Acc@1   0.00 (  1.15)   Acc@5   1.56 (  3.91)
Test: [150/157] Time  0.206 ( 0.218)    Loss 7.1213e+00 (7.1854e+00)    Acc@1   1.56 (  1.19)   Acc@5   4.69 (  3.92)
 * Acc@1 1.160 Acc@5 3.930
=> saved checkpoint for epoch 0 at /mnt/blob/data/checkpoint.pth.tar
=> best model found at epoch 0 saving to /mnt/blob/data/model_best.pth.tar
Epoch: [1][  0/782]     Time  4.317 ( 4.317)    Data  0.100 ( 0.100)    Loss 4.5235e+00 (4.5235e+00)    Acc@1   4.69 (  4.69)   Acc@5  18.75 ( 18.75)
Epoch: [1][ 10/782]     Time  0.825 ( 1.121)    Data  0.118 ( 0.096)    Loss 4.1199e+00 (4.4327e+00)    Acc@1  10.94 (  8.24)   Acc@5  31.25 ( 24.15)
```

- Verify Checkpoint in Storage Account

After each epoch is completed you could see checkpoint file updated in storage account