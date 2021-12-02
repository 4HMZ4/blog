# blog-hugo
I am hoping to migrate my dynamic blog currently hosted using GHOST CMS
to a static site using HUGO.

## Pros of static sites
* Security
* Reliability
* Speed
* Scalability
* Low Resource requirement

## Why
The reasons above all fit well with my current goal of trying to containerise
all of the services running on my home lab. Currently I have 2 servers running
Proxmox VE with a combined 48 threads and 256GB of RAM which as you can imagine
take a lot of power.

The plan is to slowly move the majority of my services to a cluster of
4 RaspberryPis. If all the services required support the ARM CPU architecture
then I can completely decommission the power hungry servers.

## How
I am still coming up with the deployment method of this site but from what I
have seen the Github CI/CD pipeline makes a lot of sense as the site will be
hosted on Github anyway.
* Update Github with new code
* Github generates a docker image
* Kubernetes cluster uses that image to host the site


## When Pulling / Cloning
``` bash
git clone --recurse-submodules https://github.com/4hmz4/blog
```

## Update the theme
``` bash
git submodule update --remote --merge
```
