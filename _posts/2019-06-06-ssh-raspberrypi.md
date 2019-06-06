---
layout: post
title: SSH into RaspberryPi through GCP VM instance
categories: [general, demo]
tags: [project, tutorial]
description: About a month ago I got my first ever personal RaspberryPi. Just like a kid who just got his first bike, I was filled with joy and excitement and wanted to play with it ...
---

About a month ago I got my first ever personal RaspberryPi. Just like a kid who just got his 
first bike, I was filled with joy and excitement and wanted to play with it 24/7. But as a 
full time student, I was often far from home, and I had to contain myself from running back to my 
apartment to play with my Pi. I found a good solution to tackle this. I decided create a 
reverse SSH tunnel from my Pi to a GCP VM instance, so that I can SSH into my Pi from the VM already had remote access to.
This is very useful, because the alternative would have been port fowarding on my home router and 
this often is very cumbersome to do and manage. So let me share how I did it.

<br/>
First, create a GCP VM instance if you do not already have one following this [guide](https://cloud.google.com/compute/docs/instances/create-start-instance)

Next, Make sure you have gcloud sdk on your Pi. If not, you can install it with these commands:
    {% highlight C %}
    # Create environment variable for correct distribution
    CLOUD_SDK_REPO="cloud-sdk-$(grep VERSION_CODENAME /etc/os-release | cut -d '=' -f 2)"

    # Add the Cloud SDK distribution URI as a package source
    echo "deb http://packages.cloud.google.com/apt $CLOUD_SDK_REPO main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list

    # Import the Google Cloud Platform public key
    curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

    # Update the package list and install the Cloud SDK
    sudo apt-get update && sudo apt-get install google-cloud-sdk
    {% endhighlight %}
Now, from your Pi, type the following command to create a reverse SSH tunnel to your VM instance. The REMOTE_PORT value will be the 
port that will be used on your VM instance to be fowarded to your Pi's port 22 (Default SSH port)
{% highlight C %}
gcloud compute --project "PROJECT_NAME" ssh --zone "VM_REGION" "VM_NAME" -- -f -N -R REMOTE_PORT:localhost:22
{% endhighlight %}
A brief explanation of the command flags: 
* -f: This runs SSH session to run in the background
* -N: Does not open a shell or execute program on the remote host
* -R: Creates a remote tunnel to your local machine (Your Pi)

That's it! Now just simply SSH into the port that you chose, and you can now access your Pi from anywhere around the world as long as 
your Pi is running and safe at home. Happy hacking!