# Simulate Spot VM Eviction 

## Virtual Machine Scale Set VMs - Simulate Eviction

- Use Azure docs console API test
https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesetvms/simulateeviction#code-try-0

- Or send REST using Azure CLI

```
az rest --verbose --method POST \
   --uri 'https://management.azure.com/subscriptions/<sub Id>/resourceGroups/MC_<ResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS Name>/virtualMachines/<InstanceID>/simulateEviction?api-version=2020-12-01'`

```

## Validate the job

Once worker on the removed Node has stoped, the running worker will detect it and adjust the training accoringly,
Notice restart_count, group_rank and epoch the process started from:

```
[ERROR] 2021-05-21 23:18:16,219 local_elastic_agent: [default] Worker group failed
Traceback (most recent call last):
  File "/opt/conda/lib/python3.7/site-packages/torchelastic/agent/server/local_elastic_agent.py", line 190, in _monitor_workers  
    if self._process_context.join(timeout=-1):
  File "/opt/conda/lib/python3.7/site-packages/torch/multiprocessing/spawn.py", line 119, in join
    raise Exception(msg)
Exception:

[INFO] 2021-05-21 23:18:16,220 api: [default] Worker group FAILED. 3/3 attempts left; will restart worker group
[INFO] 2021-05-21 23:18:16,220 api: [default] Stopping worker group
[INFO] 2021-05-21 23:18:16,220 api: [default] Rendezvous'ing worker group
INFO 2021-05-21 23:18:16,220 Attempting to join next rendezvous
INFO 2021-05-21 23:18:16,259 Added self to waiting list. Rendezvous full state: {"status": "final", "version": "9", "participants": [0, 1], "keep_alives": ["/torchelastic/p2p/run_imagenet/rdzv/v_9/rank_0", "/torchelastic/p2p/run_imagenet/rdzv/v_9/rank_1"], "num_workers_waiting": 1}
INFO 2021-05-21 23:18:16,261 Keep-alive key /torchelastic/p2p/run_imagenet/rdzv/v_9/rank_1 is not renewed.

INFO 2021-05-21 23:18:16,264 Previously existing rendezvous state changed. Will re-try joining.
INFO 2021-05-21 23:18:16,264 Attempting to join next rendezvous
INFO 2021-05-21 23:18:16,285 New rendezvous state created: {'status': 'joinable', 'version': '10', 'participants': []}
INFO 2021-05-21 23:18:16,327 Joined rendezvous version 10 as rank 0. Full state: {'status': 'joinable', 'version': '10', 'participants': [0]}
INFO 2021-05-21 23:18:16,328 Rank 0 is responsible for join last call.
INFO 2021-05-21 23:18:46,390 Rank 0 finished join last call.
INFO 2021-05-21 23:18:46,390 Waiting for remaining peers.
INFO 2021-05-21 23:18:46,391 All peers arrived. Confirming membership.
INFO 2021-05-21 23:18:46,487 Waiting for confirmations from all peers.
INFO 2021-05-21 23:18:46,489 Rendezvous version 10 is complete. Final state: {'status': 'final', 'version': '10', 'participants': [0], 'keep_alives': ['/torchelastic/p2p/run_imagenet/rdzv/v_10/rank_0'], 'num_workers_waiting': 0}
INFO 2021-05-21 23:18:46,489 Creating EtcdStore as the c10d::Store implementation
[INFO] 2021-05-21 23:18:46,501 api: [default] Rendezvous complete for workers.
Result:
        restart_count=1
        group_rank=0
        group_world_size=1
        rank stride=1
        assigned global_ranks=[0]
        master_addr=imagenet-worker-0
        master_port=53357

[INFO] 2021-05-21 23:18:46,502 api: [default] Starting worker group
=> set cuda device = 0
=> creating model: resnet18
=> loading checkpoint file: /mnt/blob/data/checkpoint.pth.tar
=> loaded checkpoint file: /mnt/blob/data/checkpoint.pth.tar
=> using checkpoint from rank: 0, max_epoch: 1
=> checkpoint broadcast size is: 93588276
=> done broadcasting checkpoint
=> done restoring from previous checkpoint
=> start_epoch: 2, best_acc1: 1.1699999570846558
Epoch: [2][   0/1563]   Time  2.800 ( 2.800)    Data  0.127 ( 0.127)    Loss 3.9720e+00 (3.9720e+00)    Acc@1  20.31 ( 20.31)   Acc@5  37.50 ( 37.50)
Epoch: [2][  10/1563]   Time  0.473 ( 0.692)    Data  0.099 ( 0.110)    Loss 4.1394e+00 (4.0961e+00)    Acc@1  15.62 ( 12.64)   Acc@5  35.94 ( 33.10)
Epoch: [2][  20/1563]   Time  0.481 ( 0.597)    Data  0.112 ( 0.114)    Loss 4.3410e+00 (4.1251e+00)    Acc@1   4.69 ( 12.05)   Acc@5  23.44 ( 31.85)
Epoch: [2][  30/1563]   Tim
```