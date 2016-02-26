---
layout: post
title:  "Docker Shell Integration (DoShIn)"
date:   2016-02-26 22:33:45 +0100
categories: doshin update
---
Note: I wrote this post after opening an [issue](https://github.com/docker/docker/issues/20702), submitting a [pull request](https://github.com/docker/docker/pull/20704) and commenting with Docker's people ( @thaJeztah, @HackToday, @calavera, @albers); it was very useful.

I want to start a container simply typing the name of the image without using the verbose Docker's cli. DoShIn will scan the Docker images and create shell functions with the name of the tags.

Just type  `ubuntu` in the bash and docker will instantiate a new container.

It i

So, why this ? A bit of background. 

With the Docker Shell Integration you can run an image diretly typing the name of the image. 
Bash completion works out of the box and you can browse them, depending on your settings.
You can run a container simply calling the name of the image, i.e.

  ubuntu

  ubuntu echo "Hello World"

a container will be started with default parameters:

  --rm
  -u user_id:user_group_id
  -i
  -t

The user can set common parameters for all containers using the env variable DOCKER_OPTS_USER.

Why this? If you have images that are considered "applications" you can run them directly form 
the shell as normal application but running inside a container; look at CMD or ENTRYPOINT in the Docker
official docs.

These are your images now available from the shell:

$(echo ${list_images} | tr " /" "_")

Images tagged as latest can be called omitting "latest".




What did you do?
I expose the name of the images and their tags as bash/shell function so that the user can directly run them from the shell i.e.:
$> ubuntu_latest echo "Hello World".
This can be useful if you want to use images/container as local binaries but without typing the whole docker run ... which can be very long sometimes. Also the bash completion works, so is simpler to explore and run your images.

How did you do it?
Creating bash functions (__docker_shell_...) to extract images names and tags using docker and wrap them in simple bash functions, when the functions are loaded from the shell the user need only to run 
docker_shell_images_integration

I placed the function into contrib/completion/bash/docker because I think it was the simple place ready to host my enhancement.
I placed my functions at the bottom of the file to separate them from the original file.

How do I see it or verify it? if contrib/completion/bash/docker is run automatically by the system then you just need to type docker_shell_images_integration and then call one of your images form the shell ubuntu
Note: I tested the it on my server ubuntu 14.04 (bash 4) and on Mac (bash3 and bash4)

A picture of a cute animal (not mandatory but encouraged) Pic
Signed-off-by: Raoul Jean Pierre Bonnal raoul.bonnal@gmail.com #



You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
