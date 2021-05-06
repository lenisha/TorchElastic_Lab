# MLADS_Spring_2021_TorchElastic_Lab

This lab introduces elastic fault tolerant distributed training on Kubernetes. With larger and larger models being trained such as RoBERTa and GPT-2, the need to scale out to a distributed cluster is increasingly important. Distributed training on multiple high performance computing instances can significantly reduce the time to train modern deep neural networks on large data and running training on low cost Azure Spot VMs will further cut the costs.
Kubernetes enables machine learning teams to run training jobs distributed across fleets of powerful GPU instances. However, typical distributed training jobs are not fault tolerant, and a job cannot continue if a node fails or is reclaimed. 
Elastic Training takes it further and enables distributed training jobs to be executed in a fault tolerant and elastic manner on Kubernetes nodes that can dynamically change, without disrupting the model training process. 

Key Learnings include:
-	How to train large scale PyTorch model on a cluster of low cost Spot VMs while retaining job reliability.
-	How to run elastic distributed Pytorch training on Kubernetes with TorchElastic operator 
-	Leverage Kubernetes auto-scaling for elastic training jobs to shorten time for training

Key Takeaways:
-	Audience will be able to apply gained knowledge to save money for the customers who are running training jobs on Azure or on premises.

# Lab Steps
- Step 1: Environment Setup (AKS + Spot VM Nodepool)
- Step 2: Adjust script for Elastic training 
- Step 3: Run Notebook script  (GPT-2) Torch Elastic training on Spot VM Pool
- Step 4: Simulate node down and verify training is unaffected


