---
layout: post
title:  "Docker Shell Integration (DoShIn)"
date:   2016-02-26 22:33:45 +0100
categories: doshin update
---

I want to start a container simply typing the name of the image without using the verbose Docker's cli. [DoShIn](https://github.com/helios/doshin) will scan the Docker images and create shell functions/alias with the name of the tags.

Just type  `ubuntu` in the bash and docker will instantiate a new container.

This can be strange but if you follow the Docker philosofy to create a single image with a precise purpose/task you will end up with an image for a single application ( redis, postgresql, mysql, ...) maybe with your own ENTRYPOINT. So for running your application you will write 

`docker run ALL_YOUR_PARAMETERS youruser/redis APP_PARAMETERS`

assuming that you want to run some application by default with Docker, it would be a lot easier to write

`redis APP_PARAMTERS`

to achieve this you can simply create an alias

`alias redis="docker run ALL_YOUR_PARAMETERS youruser/redis"`

If you maintain many images and/or you have multiple users that collaborate with you, it is also required to maintain a separate archive of aliases somewhere to be shared with the users.

Just a bit of background, I am working on a research center and we need to use and maintain software with multiple versions, so that users can pick up exactely the version of the software they need for their analyses. It is also very useful in case of reproducing/replicating scientific analyses. There are many solution for this purpose

* [module](http://modules.sourceforge.net/): this is the current impementation
* full path: calling explicitly the path of every binary neded, very long commands
* [Guix](https://www.gnu.org/software/guix/): a bit complicated but it is improving quickly and the number of software is growing
* [Anaconda](https://www.continuum.io/why-anaconda): primarly developed for Python there is a project called [Bioconda](https://bioconda.github.io/) for Bioinformatics software.
* Docker: using different images for every version. I decided to test Docker for our Bioinformatics pipelines and so I created a repository [bio-docker](https://github.com/helios/bio-docker) with specific images for each scientific software/version installed in our cluster.

Note: mine is not an official project is more our environment ported to Docker. On github there is another [BioDocker](https://github.com/BioDocker) projects maintained, maybe I will contribute to it. 

DoShIn overrides the local installation giving precedence to the docker images. 

{% highlight bash %}
helios/sicer  1.1
helios/tophat 2.1.0
tophat  latest
helios/picard 2.0.1
helios/bowtie2  2.2.6
helios/stringtie  1.2.1
helios/trimmomatic  0.33
helios/vcftools 0.1.14
helios/bcftools 1.3
helios/bcftools 1.2
helios/samtools 1.3
samtools latest
helios/samtools 1.2
helios/htslib 1.3
helios/macs2  2.1.0
helios/gatk 3.3.0
helios/fastqc 0.11.3
helios/bedtools 2.24.0
helios/cutadapt 1.8
helios/rsem 1.2.26
helios/star 2.5.1a
{% endhighlight %}

a nice example is samtools. DoShIn will create multiple functions:

`helios_samtools_1.3`
`helios_samtools_1.2`
`samtools_latest`
`samtools`

and the user can call the image as a regular local application. I have not yet tested the ability to deploy the docker in a [Swarm](https://docs.docker.com/swarm/).

I think that this, at least at my workplace, can be a solution to reduce the friction between the introduction of docker and those user that do not care about it but just want to run the applications wherever is going to run.

Note on Security: as [@albers](http://github.com/albers) [pointed out](https://github.com/docker/docker/pull/20704#issuecomment-189170800) there can be a security issue is someone tag an images with a name of another system program.


A container will be started with default parameters:
{% highlight bash %}
  --rm
  -u user_id:user_group_id
  -i
  -t
{% endhighlight %}

The user can set common parameters for all containers using the env variable `DOCKER_OPTS_USER`.

DoShIn is hosted [here](https://github.com/helios/doshin) you can load the script in your bash and enable the functionalities with 

`docker_shell_images_integration`

and then call one of your images form the shell ubuntu

I tested the it on my server ubuntu 14.04 (bash 4) and on Mac (bash3 and bash4)

This script can introduce security risks. Maybe dumping the generated fucntions/aliases and let the user decide which docker image can override the system is an option.

Note: I wrote this post after opening an [issue](https://github.com/docker/docker/issues/20702), submitting a [pull request](https://github.com/docker/docker/pull/20704) and commenting with Docker's people ([@thaJeztah](http://github.com/thaJeztah), [@HackToday](http://github.com/HackToday), [@calavera](http://github.com/calavera), [@albers](http://github.com/albers)); Thanks to them I look at my idea from a different angle.
