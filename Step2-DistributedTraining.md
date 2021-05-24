# Pytorch Distributed Elastic Training Script

In this Lab we are working with Pytorch Imagenet Training example adopted from https://github.com/pytorch/examples/blob/master/imagenet/main.py.

The current PyTorch Distributed Data Parallel (DDP) module enables data parallel training where each process trains the same model but on different shards of data. 

The focus of [PyTorch Elastic](https://github.com/pytorch/elastic), which uses Elastic Distributed Data Parallelism, is to address  issues eccountered in DDP module (such as e.g.jobs cannot continue after a node fails due to an error or transient issue; jobs cannot incorporate a node that joined later; etc)  and to enable reliable and elastic execution of these data parallel training workloads. 

## Script Changes
Following typical changes are needed in the training script to make it run with Elastic training. Example is in `examples/imagenet/main.py`

Import Module  torch.distributed.elastic ??

DataLoader ???

torch.distributed.init_process_group ??

Save/Restore checkpoints?




## References
https://pytorch.org/elastic/0.2.2/train_script.html


Proceed to [Step 3 Running Distributed Training Job](/Step3-RunJob.ipynb)