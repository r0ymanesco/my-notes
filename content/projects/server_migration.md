+++
title = "Server Migration"
author = ["Tze-Yang Tung"]
date = 2023-01-05
lastmod = 2023-01-05T15:26:05+00:00
tags = ["Projects"]
draft = false
weight = 2001
+++

## Architecture {#server_migration_a}

The current architecture of independent servers with a dashboard for monitoring usage is unsustainable.
As the number of servers have increased, adding new users becomes cumbersome as each machine needs to be individually added.
Moreover, each server runs different versions of Linux and drivers, making software updates extremely difficult.

To solve these problems, the new approach will split the problem into 3 sub-problems:

-   [User management](#server_migration_b1)
-   [Library management](#server_migration_b2)
-   [Job deployment](#server_migration_b3)

The central idea is to have a single server that manages logins for every other machine in the cluster so that each new user only needs to be added once.
We also want to reduce user dependency on the libraries installed on the servers themselves in order to make software updates easier.
Finally, in order to support the skeleton approach to server libraries, we will use Docker to launch jobs on a specific machine of the user's choice.
The dashboard currently being used to monitor GPU utilisation can continue to exist so that users know which machines are free to launch jobs to.
The next section outlines how these objectives will be accompolished.


## Solution {#server_migration_b}

Firstly, to facilitate this transition, users should be notified of a timeline for doing so and server managers should present a timetable for how this transition will be carried out.
I suggest migrating servers in batches to this new architecture to reduce disruption.
Users should be asked to backup their data.


### User management {#server_migration_b1}

In order to centralise user management, we will use the [LDAP](https://wiki.archlinux.org/title/LDAP_authentication#Client_Setup) system available on all Linux distributions.
The system uses a host server to store user login information where client servers can authenticate against.
This way, new users only need to be added to the host server.
Any machine can be the host but I recommend using denizpower as it already hosts the dashboard.

For storage, we will take all of the hard drives that are in each individual server and integrate them into a network drive (e.g., [NAS](https://en.wikipedia.org/wiki/Network-attached_storage) or just attach them to the LDAP host server).
Every user would store their data in this large hard drive so that we don't run out of space on the system drive in the client servers.


### Library management {#server_migration_b2}

Previously, due to the wide variety of Linux distributions in the cluster, each machine had different versions of the kernel, NVIDIA drivers, ...etc, which is extremely painful to update.
In fact, for all intents and purposes, we did not update machines at all after the initial installation.

To make this process easier, we will instead only install the minimum amount of libraries on each server and rely on Docker to deploy jobs with specific library requirements (see [below](#server_migration_b3)).
The minimum software requirement on each server are the following:

-   Base Linux distribution (I recommend using [Archlinux](https://archlinux.org/) because it has very comprehensive documentation for LDAP and Docker).
-   [Docker](https://www.docker.com/) (make sure the service is enabled after installation)
-   NVIDIA drivers (might not be needed, see [below](#server_migration_b3))


### Job deployment {#server_migration_b3}

As mentioned above, we will use [Docker](https://www.docker.com/) to deploy jobs.
Docker is a containerised application that allows you to specify the code and required libraries to run the code, all completely independent from the base OS installation.
This means that each user can simply build containers with the libraries that they need without having to install any of the libraries on the base OS itself.
The lab can also create containers with some basic libraries, like specific Pytorch/Tensorflow versions, that others can use as the base and add additional libraries as required.
These containers can be hosted on [Docker Hub](https://index.docker.io/) for everyone to download.
You can also specify in your container to copy the necessary files from the central storage to the client server and remove it when the job is done so that the client drive doesn't run out of space.
It may even be possible to have the NVIDIA drivers installed within the Docker container itself (see [here](https://github.com/NVIDIA/nvidia-docker/issues/871)), but it is not well documented and might require some experimentation.
Users would check on the dashboard to see which server is available and deploy jobs to the selected server.

Docker does require some effort to learn and get used to but in the long run, it will be worth it.
It's also a useful skill in industry as many tech companies use Docker to run jobs on their servers.


## New servers {#server_migration_c}

As Deniz mentioned, we will be purchasing new servers soon.
As someone that has gone through the purchasing process of many of the servers you see on the dashboard, I want to give my opinion on how I configure the hardware.

Firstly, the person to contact about purchasing new servers is Tim Brown (email: Tim.Brown@pcspecialist.co.uk).
He is our contact at PC Specialist, which is Imperial's contracted company for computer equipment purchases.
You can go to PC Specialist's [website](https://www.pcspecialist.co.uk/) to configure a machine and send it to Tim for a quote.
You can usually get a discount on the price.

Secondly, try to put as many GPUs in a single machine as possible.
This is because our jobs are not very CPU intensive so it doesn't make sense to only put 1 or 2 GPUs in a machine.
Each machine also takes up a considerable amount of space on the server rack so we want to be as space efficient as possible.
However, when you configure a machine on PC Specialist's website, they may not let you put more than a certain number of GPUs in a machine.
What you should do in that case is configure the maximum allowed by the website and then contact Tim to ask him what the maximum number allowed actually is.
Often they will have other chassis available that can hold more GPUs.

Lastly, when striking a balance between GPU compute, memory and price, consider the use case first.
So far, we have only purchased gaming grade GPUs, which offer at most 24GB memory and moderate performance.
This is because they are very affordable for what they offer, and our computing needs have not been so extreme that these GPUs cannot handle them.
Moreover, due to the low price, we can buy many of them so that we have a large number of GPUs for everyone to use.

However, recently there has been increasing demand for compute as some people have begun to train reasonably large Transformer models.
Therefore, it might make sense to purchase a few professional grade GPUs, like the [NVIDIA RTX 6000 (48GB)](https://www.scan.co.uk/products/48gb-pny-nvidia-rtx-a6000-pcie-40-x16-ampere-10752-core-336-tensor-84-rt-cores-gddr6-w-ecc-dp).
Bare in mind that such GPUs are extremely expensive and can itself cost as much as a single server that we have currently.

At the end of the day, it comes down to the budget and use cases.
If you want to ask me questions, you can reach me at my email: tzeyang.tung.work@gmail.com
