Warcbase
========

Warcbase is an open-source platform for managing web archives built on HBase. The platform provides a flexible data model for storing and managing raw content as well as
metadata and extracted knowledge. Tight integration with Hadoop provides powerful tools for analytics and data processing.

Getting Started
---------------

Once you check out the repo, build WarcBase:

```
mvn clean package appassembler:assemble
```

Ingesting WARC files:

```
$ setenv CLASSPATH_PREFIX "/etc/hbase/conf/"
$ sh target/appassembler/bin/IngestWarcFiles -dir /path/to/warc/ -name archive_name -create
```

Command-line options:

+ Use the `-dir` option to specify directory containing WARC files.
+ Use the `-name` option to specify the name of the archive (will correspond to the HBase table name).
+ Use the `-create` option to create a new table (and drop the existing table if a table with the same name exists already). Alternatively, use `-append` to add to an existing table.

Starting the browser:

```
$ setenv CLASSPATH_PREFIX "/etc/hbase/conf/"
$ sh target/appassembler/bin/WarcBrowser -port 9191 -server http://myhost:9191/
```

Navigate to `http://myhost:9191/` to browse the archive.

Get URLs
-------------

MapReduce tools are used to extract urls from webpage records in ARC format stored in local disk.
Getting URLs:

+ `MapReduceArcDemo`: takes raw webpage records as input and extracts all URLs.

To run it:

```
$ hadoop jar target/warcbase-0.1.0-SNAPSHOT-fatjar.jar org.warcbase.analysis.demo.MapReduceArcDemo -input inputDir -output outputDir
```

Building an FST for URLs
---------------------------------

Two main class implementing Lucene FST:

+ `UriMappingBuilder`: takes a list of URLs as input and builds the FST mapping.
+ `UriMapping`: load the mapping file generated by Builder and provides an API for accessing FST.

To Build Lucene FST :

```
$ sh target/appassembler/bin/UriMappingBuilder inputDirectory outputFile
```

This command will read all files under `inputDirectory` as input, build FST mapping and store in `outputFile`.

Extract Links
-----------------

Similar like Get URLs step, we extracted links between urls from raw webpage records by using Hadoop. For each url, we use an unique integer id to represent, which is generated by Lucene FST.  

+ `ExtractLinks`: takes raw webpage records and FST mapping file as input, and extracts links between urls.

To run it:

```
$ hadoop jar target/warcbase-0.1.0-SNAPSHOT-fatjar.jar org.warcbase.data.ExtractLinks -input inputDir -output outputDir -FSTMappingFile fileAddr -numReducers 1
```
