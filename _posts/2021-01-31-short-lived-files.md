---
layout: post
title:  "Short Lived Files"
date:   2021-01-31 19:26:00 -0500
permalink: /short-lived-files/
categories: trywithresources abstraction programming
---
## What is this about?
Say for example you need to download a file from the internet and do something with it like upload it to s3 and then delete it.

## How to do it?
Using temporary files.
Steps: 
1. Create a TempFile static class that implements Closeable interface like the below.
2. Create a unique file and get it's handle inside try with resources block.
3. Now you have the handle, do whatever you want and close the block.
4. That's it, you don't have to worry about deleting the file since close() will be called right after the try block is done.

_Even if the try block throws an exception close will be called. Add "int i = 9/0;" just before closing the try block_

Code Snippet
```java
public static void main(String[] args) throws IOException {
    String uri = "https://v.adsrvr.org/cndhtdu/7wcw6dq/qwc0a0hx7e2667d914e944718a329d63d42da271.webm";
    String extension = FilenameUtils.getExtension(uri);
    Path path = Files.createTempFile(Paths.get("/opt/downloaded/"), "", "." + extension);

    try (TempFile tempFile = new TempFile(path.toFile())) {
        File fileHandle = tempFile.getFile();
		FileUtils.copyURLToFile(new URL(uri), fileHandle, 1_000, 1_000);
		System.out.println("Downloaded to " + fileHandle.getAbsolutePath());

        // You have the file handle - you can upload it to s3.
    } catch (Exception e) {
        System.out.println("OOOOPS");
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
        if (!file.delete()) {
            System.out.println("Could not delete the file: " + file.getAbsolutePath());
        } else {
            System.out.println("Deleted the file: " + file.getAbsolutePath());
        }
    }
}
```