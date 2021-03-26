# Openstack installation
## Refreshing the repositories for updated builds of openstack
sudo snap refresh snapd --edge
## For installing microstack and it's dependencies
sudo snap install microstack --beta --devmode
## For initialising microstack
sudo microstack init --auto --control
## For getting the admin password for the openstack dashboard
sudo snap get microstack config.credentials.keystone-password
## web dashboard 
available at http://10.20.20.1/

# Hadoop installation and configuration

# Hadoop running the wordcount program
## Check hadoop and javac are installed
javac -version
hadoop version
## Start hadoop services
start-all.sh (if not working from any path, cd to hadoop and check in all directories to find start-all.sh)
### Create a working folder for word count
### Save the java program for word count in it
### Create a new folder inside the working folder for input data and save the text file on which word count should be run on
### Create a new folder in the working folder to hold the java class files
## Set the hadoop classpath
export HADOOP_CLASSPATH=$(hadoop classpath)
## Check if hadoop classpath is set
echo $HADOOP_CLASSPATH
## Create a directory on hdfs
hadoop fs -mkdir /wordcount
## Create a directory inside it for input
hadoop fs -mkdir /wordcount/input
### Check by going to localhost:50070 utilities -> browse file system
## Upload the input file 
hadoop fs -put <INPUT_FILE_PATH> /wordcount/input
## Compile the java code and bundle it as a a jar
javac -classpath ${HADOOP_CLASSPATH} -d <classes-folder-path> <javaprogram-path>
jar -cvf <jar-file-name> -C <classes-folder-path> .
## Run the program
hadoop jar <jar-file-path> WordCount '/wordcount/input' '/wordcount/output'
## Check the output in the browser or through terminal with the command:
hadoop dfs -cat '/wordcount/output'
