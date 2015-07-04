# WildFly
<!-- .slide: class="center" -->



## Takayoshi Kimura
## JBoss / WildFlyの中の人
### twitter: nekop



<!-- .slide: data-background="images/wf/wildfly_desktop_1280x800.jpg" -->



## What is WildFly?

- Java EE 7 applciation server
- Powerful components
- Fast startup and runtime
- Advanced features
- Active community



## History

- 2015-07-02 WildFly 9.0
- 2014-05-30 WildFly 8.1
- 2014-02-11 WildFly 8.0
- 2011-2014 JBoss AS 7.x



## History

- 2010-2011 JBoss AS 6.x
- 2008-2010 JBoss AS 5.x
- 2004-2008 JBoss AS 4.x
- 2002-2004 JBoss AS 3.x
- 2001 JBoss Inc
- 1999 JBoss Project



## Java EE 7

- JSF for quick component based web development
- JAX-RS to build REST server side application
- EJB to focus on business logic
- CDI for custom falicities
- JPA for database access
- JMS for fast messaging, event driven code



## Powerful Components

- Undertow
- RESTEasy
- Hibernate
- Infinispan
- JGroups



## Demo - Boot time



## Boot time

- 2.6 sec with default Java options
- 1.6 sec with tuned Java options



## Java VM Tuning

- For fast development cycle
  - not for production use

```
-Xverify:none
-XX:+TieredCompilation -XX:TieredStopAtLevel=1
```


## Advanced features

- HTTP/2 support
- Unified logging
- Admin CLI
- Clustering API
  - http://www.mastertheboss.com/jboss-server/jboss-cluster/monitoring-a-cluster-using-wildfly-api



## Quickstarts

- Basic quickstart
  - https://github.com/wildfly/quickstart
- Ticket Monster
  - http://www.jboss.org/ticket-monster/
  - mid-size web application



## OpenShift Online

- rhc app create wf jboss-wildfly-9
  - cd wf
  - copy your source code here
  - git push



## Launch OpenShift Button

https://blog.openshift.com/updated-openshift-deploy-buttons/

![JBossClusteringUpdate2015.jpg](http://launch-shifter.rhcloud.com/launch/LAUNCH%20ON.svg)

Example: https://launch-shifter.rhcloud.com/



## Resources

- http://wildfly.org/
- https://developer.jboss.org/wiki/WildFlyFAQJapanese
