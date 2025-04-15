# devcontainer-cloudinit
cloudinit a linux VM to be a remote docker agent for vscode devcontainers
* this is an easy way to run linux devcontainers if you are on windows or mac, and you dont need to use local docker desktop
* it can also be an alternative to vscode tunnels if you like the idea but dont trust it

TL;DR - we will:
* install docker binaries
* set the docker context to a remote machine using ssh protocol
* vscode devcontainers will use the remote machine
* the machine can be a linux virtual machine accessed from windows or macos
* the virtual machine can be easily created using multipass and configured at start up via cloud-init

https://code.visualstudio.com/docs/containers/ssh

https://stackoverflow.com/questions/24418815/how-do-i-install-docker-using-cloud-init

https://cloudinit.readthedocs.io/en/latest/reference/examples.html#including-users-and-groups

Install docker cli binaries (dont need docker desktop if you dont want it)

https://docs.docker.com/engine/install/binaries/

This is using multipass to abstract the management of virtual machines in a platform agnostic way
It uses your ssh public key from github for ssh authentication - you can also use a public key directly if preferred
Remeber to update the user-data file from this repo to reference your github username or this wont work
```
//start a new virtual machine with multipass, and bootstrap it with the cloud config from this repository
multipass launch -n devcontainer --cloud-init user-data
```

Update you .ssh/config to allow connections to the fresh machine with your github identity file
(Hostname mshome.net below is for a virtual machine setup on hyperv/windows 🤮)
```
Host devcontainer
  HostName devcontainer.mshome.net
  User vscode
  IdentityFile {{same one you use for github}}
```

Check that it works and allow the thumbprint
```
//check that it works
ssh vscode@devcontainer
```

Setup a docker context and make sure it is working
```
//connect local machine to the remote docker
docker context create devcontainer --docker "host=ssh://vscode@devcontainer"
docker context use devcontainer

//check that it works
docker container list
```

Now you can clone a repo into a remote container from vscode - and it will run on the virtualized linux machine via docker

Advanced stuff detailed here:
https://code.visualstudio.com/docs/devcontainers/devcontainer-cli


