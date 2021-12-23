---
title: "Debug Docker Builds in Travis"
created_at: Thur Dec 23 2021 15:00:00 EDT
author: Montana Mendy
layout: post
permalink: 2021-12-23-debugdockertravis
category: news
excerpt_separator: <!-- more --> 
tags:
  - news
  - feature
  - infrastructure
  - community
---

![TCI-Graphics for AdsBlogs (4)](https://user-images.githubusercontent.com/20936398/147167228-e9dfe784-779f-4da2-acc8-4effd2c463db.png)

Happy Holidays builders! Today let's debug. We'll be talking about Dockerfiles, and how we can trigger those in our crafty `.travis.yml` files. Let's get started!

<!-- more --> 

## Travis Container 

Now if you're having trouble tracking down the exact problem in a build it often helps to run the build locally. To do this you need to be using our container based infrastructure, so in turn let Docker know what image you are using on Travis CI.

## Appendix 

1. Make up your own temporary build ID

```bash
BUILDID="build-$RANDOM"
```

2. View the build log, open the show more button for `WORKER INFORMATION` and find the `INSTANCE` line: 

```bash
INSTANCE="travisci/ci-garnet:packer-1512502276-986baf0"
```
3. Then run the headless server:

```bash
docker run --name $BUILDID -dit $INSTANCE /sbin/init
```

4. Run the attached client

```bash
docker exec -it $BUILDID bash -l
```

Now you are now inside your Travis environment. Run `su - travis` to begin.

![image](https://user-images.githubusercontent.com/20936398/147276024-45a06c10-3161-466d-b9ff-776f449082d3.png)

Remember to run you build in Debug mode, you can also do this in the GUI: 

![image](https://user-images.githubusercontent.com/20936398/147276164-38cca5f8-82da-4f58-a79b-66821bc2ff97.png)


Don't forget to make sure that the step in question doesn’t completely crash the Docker build by returning a non-error exit code, e.g. in case the npm run build step in the Dockerfile fails:

```bash
RUN npm run build; exit 0
```

Classically you were able to solve this usually by adding:

```bash
export NODE_OPTIONS=--max_old_space_size=4096
```

## Annotations

Annotations are allowed in Docker for the reason of defining architecture and operating system (overriding the image’s current values), os features, and an architecture variant, this takes precedent over env vars in the Docker protocol heirarchy. An example of using annotation would look something like:

```bash
docker manifest annotate 00.00.00.000:5000/cool-ibm-test:v1 00.00.00.00:5000/
```

## Conclusion 

Now let's run your Dockerfile! 

```bash
docker run — rm -it (image name)
```

There you have it! You've just debugged a Docker image in Travis CI! 

As always remember if you have any questions, any questions at all, please email me at [montana@travis-ci.org](montana@travis-ci.org).

Happy building! 
