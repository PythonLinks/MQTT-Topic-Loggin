# MQTT Topic Logging
A Docker container to log an MQTT topic tree to a directory tree of JSON files.  The files can then be shared, processed or
served with NGINX or Apache.  

An obvious way to store time series data is in a file. Then it is easy for your favorite software to read the file, or to send the file to a colleague, or serve it with a web server. 

For a tree of time series data, it makes sense to store the data to a file system tree.  Then the data has the same organization as your MQTT Tree.  And it is then very easy for your web server to serve the right file, based only on the MQTT topic name. 

So that is what this software does.  To repeat: it is a 
Docker container to log an MQTT topic tree to a directory tree of JSON files. 

This software is in production use recording history for a tree of [Chat servers](https://PythonLinks.info/chat). 

Each MQTT message arrives as a JSON string, and gets logged to a file. The MQTT topic determines which directory the file is stored in. It is very easy to map from the MQTT topic name, to the name of the directory served by the web server. 

While MQTT provides a retained message, for chat applications one really wants to see say 6 retained messages. So there is also a file cache of the 6 most recent messages for each topic.  That is all that most chat servers really want to see.  

It seems to me to be an obvious way to record MQTT data.  I am hugely surprised that such a data logger is not already available.   There are a number of time series databases, but generally they are not hierarchical.  The great thing about doing it this way, is that when you reorganize your MQTT tree, you can also just close the files, move the branch of the file system, and  resume logging.  

## Implementation Details   
The software is implemented in Python, so it is trivial to honor most feature requests. 

## Possible Enhancements. 
This list of enhancements tells you the limits of what the software does and does not do. 
I am considering making the following changes. 

1. Optionally add a time stamp to each record. 
2. Include a web server in the docker container. 
3. Rotate the files when they get too big. 
3. Let the container respond to commands sent over MQTT.  
    1. Shut down
    2. Move a topic and the associated tree of unix files
    3. Delete a topic log.
4. Create a CSV data logger.   It is actually simpler than the JSON 
   topic logger, so very easy to do. 
5. Any other requests?   
