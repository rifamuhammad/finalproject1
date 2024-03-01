## spring-boot-maven-jmeter-performance-test

Purpose : Create a new maven profile to execute only JMeter performance tests with jmeter-maven-plugin. <br/>
Reason : jmeter-maven-plugin parses and executes all the JMeter Test scripts saved in "src/test/jmeter" directory and generates a performance report in a separate jmeter maven performance execution. <br/>

Steps to follow for preparing JMeter test scripts. <br/>
1- Download apache-jmeter-5.4.1 . <br/>
2- Start JMeter GUI. <br/>
3- Create a new Test Plan and add a new Thread Group with the following values : <br/>
– The number of Threads (users) : 10 <br/>
– Ramp-up time : 10 <br/>
– Loop count : 1 <br/>

4- Add HTTP Request to Thread Group with the following values : <br/>
- protocol : http <br/>
- server name or IP : localhost <br/>
- port number : 8080 <br/>
- HTTP Request : POST <br/>
- path : /customer-info/store/save <br/>
- body data : <br/>
  { <br/>
     "name": "jeans_store", <br/>
     "products": [ { "name": "prod1" } ] <br/>
  } <br/>

5- Add View Results Tree to HTTP Request. <br/>
6- Add HTTP Header Manager to HTTP Request with Name/Value as Content-Type=application/json. <br/>
7- Add Duration Assertion to HTTP Request with duration in milliseconds 30 to assert. <br/>
8- Create a performance test and save it as HTTP_POST_customer-info_store_save_operation.jmx file. <br/>
9- Create jmeter directory under src/test folder. <br/>
10- Copy your jmeter HTTP_POST_customer-info_store_save_operation.jmx file & move to src/test/jmeter. <br/>

![JMeter_GUI_TestPlan](doc/JMeter_GUI_TestPlan.png) <br/>

<br/>
### Local run steps <br/>
NOT : Because it is a local test do not forget to start your API before running performance tests.<br/>
1- Start Spring Boot API from main method containing class CustomerInfoApplication.java . <br/>
2- Alternatively you can start your Docker container by following the commands below. <br/>
3- To execute only jmeter performance tests from the console run the following maven command : <br/>
NOT : Execute maven command from where the pom.xml is located in the project directory. <br/>
<pre> 
$ mvn clean install -P performance-test <br/>
</pre>

[INFO] --- jmeter-maven-plugin:3.4.0:results (jmeter-check-results) @ spring-boot-maven-jmeter-performance-test --- <br/>
[INFO] <br/>
[INFO] ------------------------------------------------------- <br/>
[INFO] S C A N N I N G    F O R    R E S U L T S <br/>
[INFO] ------------------------------------------------------- <br/>
[INFO] <br/>
[INFO] Will scan results using format: CSV <br/>
[INFO] <br/> 
[INFO] Parsing results file as type: CSV <br/>
[INFO] Number of failures in 'HTTP_POST_customer-info_store_save_operation-9_2021_13_31_43.csv': 1 <br/>
[INFO] Number of successes in 'HTTP_POST_customer-info_store_save_operation-9_2021_13_31_43.csv': 9 <br/>
[INFO]  <br/>
[INFO] ------------------------------------------------------- <br/>
[INFO] P E R F O R M A N C E    T E S T    R E S U L T S <br/>
[INFO] ------------------------------------------------------- <br/>
[INFO]  <br/>
[INFO] Result (.csv) files scanned: 1 <br/>
[INFO] Successful requests:         9 <br/>
[INFO] Failed requests:             1 <br/>
[INFO] Failures:                    10.0% (2.0% accepted) <br/>
[INFO] <br/>

4- jmeter-maven-plugin results can be accessed from the application directory : "target/jmeter/results" <br/>

![jmeter_maven_plugin](doc/jmeter_maven_plugin_target_jmeter_results.png) <br/>

### Tech Stack
Java 11 <br/>
H2 Database Engine <br/>
spring boot <br/>
spring boot starter data jpa <br/>
spring boot starter web <br/>
spring boot starter test <br/>
hibernate <br/>
logback <br/>
maven <br/>
jmeter-maven-plugin <br/>
springfox-swagger-ui <br/>
datasource-proxy <br/>
Docker <br/>
<br/>

### Docker build run steps
NOT : Execute docker commands from where the DockerFile is located. <br/>
<pre>
$ docker system prune <br/>
$ docker build . --tag demo  <br/>
$ docker run -p 8080:8080 -e "SPRING_PROFILES_ACTIVE=dev" demo:latest <br/>
</pre>

## API OPERATIONS
### Save store with products successfully to database

Method : HTTP.POST <br/>
URL : http://localhost:8080/customer-info/store/save <br/>

Request : 
<pre>
curl --location --request POST 'http://localhost:8080/customer-info/store/save' \
--header 'Content-Type: application/json' \
--data-raw '{
  "name": "jeans_store",
  "products": [
    {
      "name": "prod1"
    },
    {
      "name": "prod2"
    },
    {
      "name": "prod3"
    }
  ]
}'
</pre><br/>

Response : 

HTTP response code 200 <br/>
<pre>
{
    "id": 1,
    "name": "jeans_store",
    "products": [
        {
            "id": 1,
            "name": "prod3"
        },
        {
            "id": 2,
            "name": "prod1"
        },
        {
            "id": 3,
            "name": "prod2"
        }
    ]
}
</pre>


### List Store saved to database

Method : HTTP.GET <br/>
URL : http://localhost:8080/customer-info/store/list <br/>

Request : 
<pre>
curl --location --request GET 'http://localhost:8080/customer-info/store/list'
</pre><br/>

Response : 

HTTP response code 200 <br/>
<pre>
[
    {
        "id": 1,
        "name": "jeans_store",
        "products": [
            {
                "id": 1,
                "name": "prod3"
            },
            {
                "id": 2,
                "name": "prod1"
            },
            {
                "id": 3,
                "name": "prod2"
            }
        ]
    }
]
</pre><br/>
