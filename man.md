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

sudo apt-get install update
sudo apt-get update
sudo apt-get install default-jdk
java -version
sudo addgroup  hadoop
sudo adduser —ingroup hadoop hadoopuser
sudo adduser hadoopuser sudo
sudo apt-get install opsenssh-server
su - hadoopuser (try without hyphen if it doesn't work)
ssh-keygen -t rsa -P  “”
cat $HOME/.ssh/id_rsa.pub >> $HOME/.ssh/authorized_keys
ssh localhost
exit

## INSTALL HADOOP 2.9.0 tar.gz file

cd /home/megh/Desktop 

### EXTRACT THE .gz FILE

sudo mv hadoop-2.9.0  /usr/local/hadoop
sudo chown -R hadoopuser  /usr/local  
sudo gedit ~/.bashrc 

### End of bashrc file Add :
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export HADOOP_HOME=/usr/local/hadoop
export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib"


source ~/.bashrc 

sudo gedit /usr/local/hadoop/etc/hadoop/hadoop-env.sh
### Comment out JAVA_HOME and add:
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64 // check the java version 

sudo gedit /usr/local/hadoop/etc/hadoop/core-site.xml
### INSIDE CONFIGURATION ADD:
<property>
<name>fs.default.name</name>
<value>hdfs://localhost:9000</value>
</property>

 
sudo gedit /usr/local/hadoop/etc/hadoop/hdfs-site.xml
### INSIDE CONFIGURATION ADD:
<property>
<name>dfs.replication</name>
<value>1</value>
</property>
<property>
<name>dfs.namenode.name.dir</name>
<value>file:/usr/local/hadoop_tmp/hdfs/namenode</value>
</property>
<property>
<name>dfs.datanode.data.dir</name>
<value>file:/usr/local/hadoop_tmp/hdfs/datanode</value>
</property>

sudo gedit /usr/local/hadoop/etc/hadoop/yarn-site.xml
### INSIDE CONFIGURATION ADD:
<property>
<name>yarn.nodemanager.aux-services</name>
<value>mapreduce_shuffle</value>
</property>
<property>
<name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
<value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>


sudo cp  /usr/local/hadoop/etc/hadoop/mapred-site.xml.template /usr/local/hadoop/etc/hadoop/mapred-site.xml

sudo gedit /usr/local/hadoop/etc/hadoop/mapred-site.xml
### INSIDE CONFIGURATION ADD:
<property>
<name>mapreduce.framework.name</name>
<value>yarn</value>
</property>

sudo mkdir -p usr/local/hadoop_space 

sudo mkdir -p usr/local/hadoop_space/hdfs/namenode
sudo mkdir -p usr/local/hadoop_space/hdfs/datanode
sudo chown -R hadoopusr  /usr/local/hadoop_space 
hdfs namenode -format
start-dfs.sh
start-yarn.sh
jps

Check after going to localhost:50070

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
