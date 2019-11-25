# MQTT Topic Logging
A docker container to log a tree of MQTT topics to a file system tree. Serve the files  with your favorite web server. 

This software is in production recording history for a tree of [Chat servers](https://PythonLinks.info/chat). 

Each MQTT message arrives as a JSON string, and gets logged to a file.
The MQTT topic determines which directory the file is stored in.  Which determines the URL to 
access the files. The JSON file is then served by your NGINX or Apache Web server. 

There is also a cache of the 6 most recent messages for each topic.  That is all that most chat servers really want to see.  

I will be packaging it up and releasing it as a Docker container. 

It seems to me to be an obvious way to record MQTT data.  I am hugely surprised that such a data logger is not already available.   There are a number of time series databases, but generally they are not hierarchical.  The great thing about doing it this way, is that when you reorganize your MQTT tree, you can also just close the files, move the branch of the file system, and  resume logging.  

The software meets my needs.  I am happy to make the changes that other people want. 
