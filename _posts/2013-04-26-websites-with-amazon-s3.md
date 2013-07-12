---
layout: post_entry
image: /img/bikes.jpg
title: First Post! Getting a website up on Amazon S3
---

Going to try to learn how to move my domain/web hosting from a shared service to Amazon. Specifically, we'll look at using Amazon's S3 file hosting service and how it can be used to host a static website.
<!--more-->

After you've acquired your domain (not through Amazon as they don't do domain registrations), you need to create an S3 bucket to hold the files for your site.

![AWS Console](/img/2013-04-26-aws-console.png "The ever growing list of Amazon web services")

Log into your [AWS Console](http://console.aws.amazon.com) and select the S3 service.

![S3 settings](/img/2013-04-26-s3-primary.png)

Create a bucket with the name of your domain (without the leading 'www'). In my case 'circumstanceconspired.net'. Select your new bucket and view the 'Properties'.  Select the 'Static Website Hosting' section and enable website hosting. Give it a value of 'index.html' for your 'Index Document'. Hit save.

![S3 redirect](/img/2013-04-26-s3-redirect.png)

Now create a new S3 bucket, but this time add the 'www.' to the beginning of the bucket name (ie. 'www.circumstanceconspired.net'). Go to the properties of this new bucket, and again to the 'Static Website Hosting' section. Fill out the 'Redirect all requests to another host name' so that this new bucket redirects to the first bucket you created. In my case the redirect goes to my bucket named 'circumstanceconspired.net'.

![S3 endpoint](/img/2013-04-26-s3-endpoint.png)

Before hitting 'Save', copy the full name of the 'Endpoint' in the 'Static Website Hosting' section of the properties of your bucket (you'll need this soon).

Now switch to the Amazon console and select the Route 53 service. Create a hosted zone, and put your domain name in the Domain Name field. Select your new entry and select 'Go to Record Sets'. You should see two entries.  One of type NS and another of type SOA.

![A record](/img/2013-04-26-r53-a.png)

Press 'Create a Record Set'. Leave the 'Name' field alone and set the 'Type' to 'A - IPv4 address'. Alias:'Yes', and in the 'Alias Target' field you should select the S3 bucket you just created (not the one with the 'www.' in the name). Save it.

![A record](/img/2013-04-26-r53-cname.png)

Create another record. This time set the 'Name' field to 'www' (and your domain name will be added to the right of your text in grey. The 'Type' should be CNAME and 'Alias' set to No. The value should be set to the full name of your S3 bucket with the leading 'www.' in the name. This is the 'Endpoint' text you copied from the properties view when creating your 'www.' bucket. Save it.

Now go back select the record entry of type 'NS'. On the side, it will list several servers next to the 'Value' field. Use these servers to override the default name servers that your domain registration provider uses (these steps are specific to whatever company you use to register your domain).

Once all this is set up, it can take a day or so for the DNS entries to propogate so you may not be able to use the S3 site hosting using your domain name just yet.

But as a simple test that things are (mostly) working, go back to your main S3 bucket (no 'www.') and upload an HTML file. I used this as an example:

    <!DOCTYPE html>
    <html>
    <head>
    	<title>Test</title>
    </head>
    <body>
     <p>Testing S3</p>
    </body>
    </html>

Now you should be able to go to the Endpoint name you copied previously (should be your domain name with something like '.s3-website-us-east-1.amazonaws.com' appended to it), and S3 should serve up the above page!
