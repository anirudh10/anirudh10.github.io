---
layout: post
title:  "Temporary Files 2"
date:   2021-03-20 09:49:00 -0500
permalink: /temporary-files-2/
categories: trywithresources abstraction programming
---
## Use Case
Suppose your manager tells you - "Yo Sadio, write me a tool that takes a url and uploads its content to s3."

## Thinking process
By the looks of it, he doesn't care about where you download it, how you upload it or what you do with the content later on.
Let's see what Java has for us - temporary files!

## How to do it?
The tempfile is deleted manually in the /temporary-files-1 version. 
This version uses try with resources to delete it.
1. Create a static class (TempFile) that implements the "Closeable" interface.
2. Create the tempfile object with try and resources.
3. Now you have the handle, do whatever you want and close the block.
4. That's it, you don't have to worry about deleting the file since close() will be called right after the try block is done.

Code Snippet
```java
// Try with Resources
public static void main(String[] args) throws IOException {
    // 0. Setup!
    File file = File.createTempFile("prefix", null);

    String urlString = "https://v.adsrvr.org/cndhtdu/7wcw6dq/qwc0a0hx7e2667d914e944718a329d63d42da271.webm";
    URL url = new URL(urlString);

    try (TempFile tempFile = new TempFile(file)) {
        // 1. download to local
        FileUtils.copyURLToFile(url, tempFile.getFile());

        // 2. upload to s3
        //PutObjectRequest por = new PutObjectRequest(BUCKET_NAME, file.getName(), file);
        //amazonS3Client.putObject(por);
    }
}

public static class TempFile implements Closeable {
    private final File file;
    public TempFile(File file) {
        this.file = file;
    }
    public File getFile() {
        return file;
    }
    @Override
    public void close() {
        if (file.delete()) {
            System.out.println("Deleted the file: " + file.getAbsolutePath());
        } else {
            System.out.println("Could not delete the file: " + file.getAbsolutePath());
        }
    }
}
```