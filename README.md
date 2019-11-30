# MQTT Topic Logging
A Docker container to log an MQTT topic tree to a directory tree of JSON files.  The files can be 
served with NGINX or Apache. 

An obvious way to store time series data for a single topic is in a file.  When the file gets too big, maybe rotate the files.  For a tree of time series data, it makes sense to store the data to a file system tree. 
Then it is trivial to figure out the name of the file to serve based on the MQTT Topic name. 

This software is in production use recording history for a tree of 
[Chat servers](https://PythonLinks.info/chat). 

Each MQTT message arrives as a JSON string, and gets logged to a file.
The MQTT topic determines which directory the file is stored in. It is very easy to map 
from the MQTT topic name, to the name of the directory stored by the web server. 

There is also a file cache of the 6 most recent messages for each topic.  
That is all that most chat servers really want to see.  

It seems to me to be an obvious way to record MQTT data.  I am hugely surprised that such a data logger is not already available.   There are a number of time series databases, but generally they are not hierarchical.  The great thing about doing it this way, is that when you reorganize your MQTT tree, you can also just close the files, move the branch of the file system, and  resume logging.  

## Possible Enhancements. 
This list of enhancements tells you the limits of what the software does and does not do. 
I am considering making the following changes. 

1. Add a time stamt to each record. 
2. Include a web server in the docker container. 
3. Rotate the files when they get too big. 
3. Let the container respond to commands sent over MQTT.  
    1. Shut down
    2. Move a topic and the associated tree of unix files
    3. Delete a topic log.
4. Support csv data.
5. Any other requests?    
