# bluefin-dx - The Bluefin Developer Experience

Dedicated developer image with bundled tools. It endevaours to be the world's most powerful cloud native developer environment. :) It includes everything in the base image plus the following tools:

## Visual Studio Code

[Visual Studio Code](https://code.visualstudio.com/) is included on the image to facilitate local development. 

## Devpod 

[DevPod](https://devpod.sh/docs/what-is-devpod) is included to provide infrastructure-independent and client-only reproducible developer environments, powered by [devcontainers](https://containers.dev/)
 
- Follow [Quickstart VS Code](https://devpod.sh/docs/getting-started/quickstart-vscode) to set up your envitronment.
  
## Devbox

Nix-powered Development Experience powered by [Devbox](https://www.jetpack.io/devbox) and [Fleek](https://getfleek.dev)

- `just nix-devbox` to get started
- `just nix-devbox-global` to install a global profile

## Containerized Development with Distrobox

- Built-in Ubuntu user space 
    - `Ctrl`-`Alt`-`u` - will launch an Ubuntu image inside a terminal via [Distrobox](https://github.com/89luca89/distrobox), your home directory will be transparently mounted
    - A [BlackBox terminal](https://www.omgubuntu.co.uk/2022/07/blackbox-gtk4-terminal-emulator-for-gnome) is used just for this configuration
    - Use this container for your typical CLI needs or to install software that is not available via Flatpak or Fedora
    - Optional [ubuntu-toolbox image](https://github.com/ublue-os/bluefin/pkgs/container/ubuntu-toolbox) with Python, and other convenience development tools. `just distrobox-bluefin` to get started. To configure `just` follow the [guide](https://ublue.it/guide/just/).
    - Optional [universal image](https://mcr.microsoft.com/en-us/product/devcontainers/universal/about) with Python, Node.js, JavaScript, TypeScript, C++, Java, C#, F#, .NET Core, PHP, Go, Ruby, and and Conda. `just distrobox-universal` to get started
    - `just assemble` shortcut to decleratively build distroboxes defined in `/etc/distrobox/distrobox.ini`
    - Refer to the [Distrobox documentation](https://distrobox.privatedns.org/#distrobox) for more information on using and configuring custom images
    - GNOME Terminal
      - `Ctrl`-`Alt`-`t` - will launch a host-level GNOME Terminal if you need to do host-level things in Fedora (you shouldn't need to do much).   

# Kubernetes and other Cloud Native Tooling

- [kind](https://kind.sigs.k8s.io/) - Run a Kubernetes cluster on your machine. Do a `kind create cluster` on the host to get started!
    - [kubectl](https://kubernetes.io/docs/reference/kubectl/) - Administer Kubernetes Clusters
    - helm, ko, flux, minio-client -- if it's an incubated project we intend to add it where appropriate

# Virtualization and Container Runtimes

- [virt-manager](https://virt-manager.org/) and associated tooling
- Podman and Docker extras
  - Automatically aliases the `docker` command to `podman`
  - podman.socket on by default so existing tools expecting a Docker socket work out of the box
- [LXC](https://linuxcontainers.org/) and [LXD](https://ubuntu.com/lxd) provide system containers

# Machine Learning

Bluefin includes a NGC container that includes the latest [stable PyTorch from Nvidia](https://catalog.ngc.nvidia.com/orgs/nvidia/containers/pytorch) on top of Ubuntu LTS. It includes python3, pip3 , pandas, matplotlib, and scikit-learn. The additional pip packages are commonly used ones but not comprehensive. Only pip is used and mamba or conda were not tested yet.

You must already be using `bluefin-dx-nvidia` as its meant for those GPUs and has nvidia container toolkit support. 
Try running `nvidia-smi` in a regular `distrobox create --nvidia -i ubuntu` to check that all works fine if you want to test before downloading the large nvidia container. 

To create the box use the following command. Note that this container is large (20+ GB!):

    just distrobox-mlbox

To enter the working environment:

    distrobox enter mlbox
    
Then the init-hooks will run once only after which you should be able to run:

    nvidia-smi

To check if GPUs are seen and enter the python repl to run:

    import torch;torch.cuda.is_available()

Some possible tests can be to run a transformers inference or training job or git clone a pytorch benchmarks repo and run single or multi gpu commands: E.g. to test multi-gpu setup on two 3090s:

    ´´´
    git clone https://github.com/aime-team/pytorch-benchmarks.git
    ´´´
    run
    ´´´
    python3 main.py --num_gpus 2 --compile --model bert-large-uncased --data_name squad --global_batch_size  24
    ´´´

On other operating systems use [this .ini file](https://github.com/ublue-os/bluefin/blob/730f39caae21e48fb91f00010cf0cf8d32ee44bd/dx/usr/share/ublue-os/distrobox/pytorch-nvidia.ini) and run:

    distrobox assemble create --file /path/to/your/mlbox.ini

# Quality of Life Improvements

- [Cockpit](https://cockpit-project.org/) for local and remote management 
- A collection of well curated monospace fonts 
- hashicorp repo included and enabled
  - None of them installed by default, but you can just add them to the Containerfile as you need them
    - systemd shutdown timers adjusted to 15 seconds
    - [Tailscale](https://tailscale.com/) for VPN
    - [Just](https://github.com/casey/just) task runner for post-install automation tasks. Check out [our documentation](https://universal-blue.org/guide/just/) for more information on using and customizing just.
    - `fish` and `zsh` available as optional shells, use `just fish` or `just zsh` and follow the prompts to configure them
