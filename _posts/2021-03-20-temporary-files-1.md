---
layout: post
title:  "Temporary Files 1"
date:   2021-03-20 09:49:00 -0500
permalink: /temporary-files-1/
categories: programming
---
## Use Case
Suppose your manager tells you - "Yo Sadio, write me a tool that takes a url and uploads its content to s3."

## Thinking process
By the looks of it, he doesn't care about where you download it, how you upload it or what you do with the content later on.
Let's see what Java has for us - temporary files!

## How to do it?
Simple download and delete: 
1. Create a temporary file.
2. Download the url content to it.
3. Delete the file.

Code Snippet
```java
// 0. Setup!
File file = File.createTempFile("prefix", null);

String urlString = "https://v.adsrvr.org/cndhtdu/7wcw6dq/qwc0a0hx7e2667d914e944718a329d63d42da271.webm";
URL url = new URL(urlString);

// 1. download to local
FileUtils.copyURLToFile(url, file);
System.out.println(file.getName());

// 2. upload to s3
//PutObjectRequest por = new PutObjectRequest(BUCKET_NAME, file.getName(), file);
//amazonS3Client.putObject(por);

// 3. delete the file
System.out.println(file.delete());
```