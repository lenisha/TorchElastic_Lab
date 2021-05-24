# Pytorch Distributed Elastic Training Script

In this Lab we are working with Pytorch Imagenet Training example adopted from https://github.com/pytorch/examples/blob/master/imagenet/main.py.

The current PyTorch Distributed Data Parallel (DDP) module enables data parallel training where each process trains the same model but on different shards of data. 

The focus of [PyTorch Elastic](https://github.com/pytorch/elastic), which uses Elastic Distributed Data Parallelism, is to address  issues eccountered in DDP module (such as e.g.jobs cannot continue after a node fails due to an error or transient issue; jobs cannot incorporate a node that joined later; etc)  and to enable reliable and elastic execution of these data parallel training workloads. 

## Script Changes
Following typical changes are needed in the training script to make it run with Elastic training. Example is in `examples/imagenet/main.py`

DataLoader uses ElasticDistributedSampler instead of RandomSampler or DistributedSampler. Elastic Distributed Sampler is derived from Distributed Sampler and guarantess each worker will load Dataset that is exclusive to that worker. [Source code](https://github.com/pytorch/pytorch/blob/master/torch/distributed/elastic/utils/data/elastic_distributed_sampler.py)

Training script should implement load_checkpoint and save_checkpoint routines. When a worker fails, it is restarted from a checkpoint using load_checkpoint routine. Checkpoints can be stored either locally or to a NFS mounted in the worker. It is preferrable to save the checkpoint in NFS storage such as Azure Blob, as the most recent checkpoint will be visible to all the workers. When saving checkpoint locally on a VM additional logic is needed to broadcast the checkpoint to all the workers joining the rendezvous from the existing worker.

Elastic training is launched using torchelastic.distributed.launch instead of torch.distributed.launch. This module requires three additional arguments: 
rdzv_id: a unique job id that is shared by all the workers,
rdzv_backend: backend such as etcd to synchronize the workers,
rdzv_endpoint: port where backend is running.


## References
https://pytorch.org/elastic/0.2.2/train_script.html


Proceed to [Step 3 Running Distributed Training Job](/Step3-RunJob.ipynb)
