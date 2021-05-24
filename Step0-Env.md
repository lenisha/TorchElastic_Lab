# Environment Setup

To run this Lab you will need Jupiter Notebook environment, we recomment to use 

- VS Code with Windows Sybsystem for Linux. Follow steps descibed in [Remote development in WSL](https://code.visualstudio.com/docs/remote/wsl-tutorial) to setup environment.
- Install Python and Python extension in VS Code as decribed in [Python in VS Code](https://code.visualstudio.com/docs/python/python-tutorial)

Another options include
- Spinning up Linux DSVM in Azure - [Set up the Data Science Virtual Machine for Linux](https://docs.microsoft.com/en-us/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)
- Creating [Azure ML Compute Instance](https://docs.microsoft.com/en-us/azure/machine-learning/concept-compute-instance) and install [VS Code AML Extension](https://docs.microsoft.com/en-us/azure/machine-learning/tutorial-setup-vscode-extension)

- You could also run all commands from the lab in **Azure Cloud Shell** without installing anything locally, Jupiter notebooks are provided for easiest experience

## Prerequisites
Install following tools in WSL (or other Linux environment you havechosen)

- Azure CLI - [Install the Azure CLI on Linux](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-linux?pivots=apt)
  ```sh
  curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
  ```

- Kubectl - [Install and Set Up kubectl on Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)
  ```sh
  sudo apt-get update
  sudo apt-get install -y apt-transport-https ca-certificates curl
  sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
  echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
  sudo apt-get update
  sudo apt-get install -y kubectl
  ```

- Helm - [Installing Helm](https://helm.sh/docs/intro/install/#from-apt-debianubuntu)
   ```sh
   curl https://baltocdn.com/helm/signing.asc | sudo apt-key add -
   sudo apt-get install apt-transport-https --yes
   echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
   sudo apt-get update
   sudo apt-get install helm
   ```

- Python - install python to work with VS Code Python extension


  ## Run the Notebooks

  - Clone this github repo
  
  Open [Step 1 Kubernetes Infrastructure Setup](/Step1-Setup.ipynb) Kubernetes Infrastructure Setup in VS Code (or Jypiter server you setup in  DVSM, AML)