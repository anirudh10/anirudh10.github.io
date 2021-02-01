---
layout: post
title:  "TempFile or Short Lived Files"
date:   2021-01-31 19:26:00 -0500
categories: programming, abstraction, trywithresources
---
What is this about?
Say for example you need to download a file from the internet and do something with it like upload it to s3 and then delete it.

How to do it?
Using temporary files.
Steps: 
a) Create a TempFile static class that implements Closeable interface like the below.
b) Create a unique file and get it's handle inside try with resources block.
c) Now you have the handle, do whatever you want and close the block.
d) That's it, you don't have to worry about deleting the file since close() will be called right after the try block is done.

Even if the try block throws an exception close will be called.
Try pluggin in int i = 9/0; just before try closes.

Code Snippet 1
{% highlight java %}
public static void main(String[] args) throws IOException {
    String uri = "https://v.adsrvr.org/cndhtdu/7wcw6dq/qwc0a0hx7e2667d914e944718a329d63d42da271.webm";
    String extension = FilenameUtils.getExtension(uri);
    Path path = Files.createTempFile(Paths.get("/opt/downloaded/"), "", "." + extension);

    try (TempFile tempFile = new TempFile(path.toFile())) {
        FileUtils.copyURLToFile(new URL(uri), tempFile.getFile(), 1_000, 1_000);
        System.out.println("Downloaded to " + tempFile.getFile().getAbsolutePath());

        // You have the file handle: tempFile.getFile() - do whatever you want with it like upload to s3.
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
{% endhighlight %}