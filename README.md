 # MQTT Simple Topic Logger
[A Docker container](https://hub.docker.com/repository/docker/pythonlinks/simple-topic-logger) to log an MQTT topic tree to a directory tree of JSON files.  The files can then be shared, processed or served with NGINX or Apache.  

An obvious way to store time series data is in a file. Then it is easy for your favorite software to read the file, or to send the file to a colleague, or serve it with a web server. 

For a tree of time series data, it makes sense to store the data to a file system tree.  Then the data has the same organization as your MQTT Tree.  And it is then very easy for your web server to serve the right file, based only on the MQTT topic name. 

So that is what this software does.  To repeat: it is a 
Docker container to log an MQTT topic tree to a directory tree of JSON files. 

This software is in production use recording history for a tree of [Chat servers](https://PythonLinks.info/chat). 

Each MQTT message arrives as a JSON string, and gets logged to a file. The MQTT topic determines which directory the file is stored in. It is very easy to map from the MQTT topic name, to the name of the directory served by the web server. 

While MQTT provides a retained message, for chat applications one really wants to see say 6 retained messages. So there is also a file cache of the 6 most recent messages for each topic.  That is all that most chat servers really want to see.  

It seems to me to be an obvious way to record MQTT data.  I am hugely surprised that such a data logger is not already available.   There are a number of time series databases, but generally they are not hierarchical.  The great thing about doing it this way, is that when you reorganize your MQTT tree, you can also just close the files, move the branch of the file system, and  resume logging.

##TO RUN THIS CONTAINER

First Create a directory for the data
$mkdir Data
That way you have permissions on that directory. 
Now create the config.py file
$docker run  -d  --name logger -v /Path/To/My/Directory/Data:/app/data  -t pythonlinks/simple-topic-logger:latest config

STOP THE CONTAINER
$docker stop logger

You can then edit the configuration file in 
/Path/To/Your/Data/Directory/Data/config.py

`
class Config(object):
    userName = 'MyUserName'
    password = 'MyPassword'
    topics = ['MyTopic']
    qos = 0

Then restart the container

docker start logger

If you now look at 
Data/MyTopic/log.json

It should be
[{"text":"Hello World"}]

You are now free to migrate to your own MQTT server, or change your topics to be logged. 
Wildcard logging also works.  Creates a tree of files. 

Remember containers and users have different file permissions. 
You may not have permissions on some of the files. 
So just enter the container
docker exec -it logger /bin/bash
And you can dlete whichever files you want. 

##Architecture
The first release of this software is written in Python   If there is demand, I would love to port it to GoLang. 
The software has a single connection to a single MQTT server  It subscribes to a list of topics.  
The MQTT connection runs in one thread, each topic runs in its own thread.  When a message arrives the MQTT thread checks to see if such a topic thread exists, if not it create a topic for that thread.  Then it puts the message into the queue for that thread.  The thread writes out the messages.   Shut down is handled correclty. 

## Scalability
I expect that this software will be used by lots of small organizations hosting their 
own Simple Topic Loggers. In that sense it is infinitely scalable.  

There are three types of scalability.  
1. **Scalable across organizations**  This software is a docker container so very easy to have lots oforganizations use it. 
2. **Scalability in Files** This software is targetted at scalability in number of files, particularly on hard disk drives. 
3. **Scalability in Records**  To increase performance, you can use multiple simple Topic Loggers on different branches of your MQTT Topic tree.  I am considering rewriting this in GoLang.  

But the money and the focus appears to be on the big companies.   How well will this scale for them?    If you want to use multiple MQTT connections, or record to different drives,  you can use multiple Simple MQTT Topic Loggers, one for each branch of the tree, possibly on different drives or servers.  It is pretty easy to assign as many of these as you want to different branches of the MQTT Tree.  If there is demand, I will release a version which which supports multiple loggers, possibly even running on different servers. 

## Possible Enhancements. 

This list of enhancements tells you the limits of what the software does and does not do. 
I am considering making the following changes listed below.  It all depends on what people want. 

1. Including a web server in the container.  
2.  Including an MQTT broker in the container.  
3.  Including the [Forest Chat Wiki](ForestWiki.com) in the container. 
1. Optionally add a time stamp to each record. 
3. Rotate the files when they get too big. 
3. Let the container respond to commands sent over MQTT.  
    1. Shut down
    2. Move a topic and the associated tree of unix files
    3. Delete a topic log.
4. Create a CSV data logger.  Let the user choose which one they want to use. 
It is actually simpler than the JSON topic logger, so very easy to do. 
5. Any other requests? 
