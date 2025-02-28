# Apache Tika JAX-RS Server

https://issues.apache.org/jira/browse/TIKA-593

Running
-------
```
$ java -jar tika-server/target/tika-server.jar --help
   usage: tikaserver
    -?,--help           this help message
    -h,--host <arg>     host name (default = localhost)
    -l,--log <arg>      request URI log level ('debug' or 'info')
    -p,--port <arg>     listen port (default = 9998)
    -s,--includeStack   whether or not to return a stack trace
                        if there is an exception during 'parse'
```
Running via Docker
------------------
Assuming you have Docker installed, you can build you own local image using the:

`mvn dockerfile:build`

The image will be named apache/tika with the tag being the version being built.
For example, building Apache Tika Server 1.17 will result in an image of `apache/tika-server:1.17`

You can then run this image by executing the following, replacing `1.17` with your build version:

`docker run -d -p 9998:9998 apache/tika-server:1.17`

This will load Apache Tika Server and expose its interface on:

`http://localhost:9998`

Usage
-----
Usage examples from command line with `curl` utility:

* Extract plain text:  
`curl -T price.xls http://localhost:9998/tika`

* Extract text with mime-type hint:  
`curl -v -H "Content-type: application/vnd.openxmlformats-officedocument.wordprocessingml.document" -T document.docx http://localhost:9998/tika`

* Get all document attachments as ZIP-file:  
`curl -v -T Doc1_ole.doc http://localhost:9998/unpacker > /var/tmp/x.zip`

* Extract metadata to CSV format:  
`curl -T price.xls http://localhost:9998/meta`

* Detect media type from CSV format using file extension hint:  
`curl -X PUT -H "Content-Disposition: attachment; filename=foo.csv" --upload-file foo.csv http://localhost:9998/detect/stream`


HTTP Return Codes
-----------------
`200` - Ok  
`204` - No content (for example when we are unpacking file without attachments)  
`415` - Unknown file type  
`422` - Unparsable document of known type (password protected documents and unsupported versions like Biff5 Excel)  
`500` - Internal error  

