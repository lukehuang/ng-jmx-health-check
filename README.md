# JMX command-line client to access the Spring Boot health endpoint

First, start your spring boot process with JMX remote agent enabled.
See http://docs.oracle.com/javase/8/docs/technotes/guides/management/agent.html

Then, access the jmx port (9010 in the example below) from this command-line client.

Example usage:

```
./gradlew build
java -Xmx20m -Xms20m -XX:+UseParallelGC -jar build/libs/jmx_spring_health-1.0.jar -U service:jmx:rmi:///jndi/rmi://localhost:9010/jmxrmi -O org.springframework.boot:type=Endpoint,name=healthEndpoint -o getData
```

## Usage

```
Usage: check_jmx -U <service_url> -O <object_name> 
  -o <operation_name> [--username <username>] [--password <password>] [-h]


Options are:

-h
    Help page, this page.
	
-U 
    JMX URL; for example: "service:jmx:rmi://<host>:<port>/jndi/rmi://<host>:<port>/jmxrmi"
	
-O 
    Object name to be checked, for example, "java.lang:type=Memory"
    
-o
    Operation to invoke on MBean after querying value. Useful to
    reset any statistics or counter.

--username
    Username, if JMX access is restricted; for example "monitorRole"
	
--password
    Password
```


## In Action

You can use the result printed out on STDOUT, but for health checking it is more advisable to use the process exit code. Here is an example:

```
status=DOWN, diskSpace={status=UP, total=190163431424, free=16598224896, threshold=10485760}, rabbit={status=DOWN, error=org.springframework.amqp.AmqpConnectException: java.net.ConnectException: Connection refused}, refreshScope={status=UP}, configServer={status=UNKNOWN, error=no property sources located}
```

Process exit codes: 
* 0=OK - The JMX result does not have a "status", or the status is "UP".
* 1=CRITICAL - The JMX result has a "status" property, and it is not "UP".
* 2=UNKNOWN - The command-line arguments are incorrect.

## Acknowledgements

* Code derived from nagios JMX plugin. See https://sourceforge.net/projects/nagioscheckjmx/

