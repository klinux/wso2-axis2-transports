# How to use the OracleAQ JMS Implementation

## TransportReceiver:
```xml
<transportReceiver name="oracleaq" class="org.apache.axis2.transport.oracleaq.OracleAQListener">
    <parameter name="ERPConnectionFactory" locked="false">
        <parameter name="java.naming.factory.initial" locked="false">oracle.jms.AQjmsInitialContextFactory</parameter>
    	<parameter name="transport.oracleaq.ConnectionFactoryJNDIName" locked="false">TopicConnectionFactory</parameter>
    	<parameter name="db_url" locked="false">jdbc:oracle:thin:@HOST:PORT/DB</parameter>
    	<parameter name="java.naming.security.principal" locked="false">USER</parameter>
    	<parameter name="java.naming.security.credentials" locked="false">PASS</parameter>
    </parameter>
</transportReceiver>
```

## Service:
### Get messages from OracleAQ and put into RabbitMQ.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<proxy xmlns="http://ws.apache.org/ns/synapse"
       name="TestAqBridge"
       startOnLoad="true"
       statistics="enable"
       trace="disable"
       transports="oracleaq">
   <target>
      <inSequence>
         <property name="FORCE_SC_ACCEPTED"
                   scope="axis2"
                   type="STRING"
                   value="true"/>
         <property name="OUT_ONLY" scope="default" type="STRING" value="true"/>
         <send>
            <endpoint key="RabbitEndpoint"/>
         </send>
      </inSequence>
      <outSequence>
         <send/>
      </outSequence>
      <faultSequence>
         <property name="ROLLBACK" scope="axis2" type="STRING" value="true"/>
      </faultSequence>
   </target>
   <parameter name="transport.oracleaq.SubscriptionDurable">true</parameter>
   <parameter name="transport.oracleaq.DestinationType">topic</parameter>
   <parameter name="transport.oracleaq.ReceiveTimeout">60000</parameter>
   <parameter name="transport.oracleaq.DurableSubscriberClientID">AGENT</parameter>
   <parameter name="transport.oracleaq.DurableSubscriberName">AGENT</parameter>
   <parameter name="transport.oracleaq.ConnectionFactory">ERPConnectionFactory</parameter>
   <parameter name="transport.oracleaq.ConnectionFactoryJNDIName">TopicConnectionFactory</parameter>
   <parameter name="transport.oracleaq.ContentType">
      <rules xmlns="">
         <jmsProperty>contentType</jmsProperty>
         <default>text/xml</default>
      </rules>
   </parameter>
   <parameter name="transport.oracleaq.MaxJMSConnections">20</parameter>
   <parameter name="transport.oracleaq.ConcurrentConsumers">20</parameter>
   <parameter name="transport.oracleaq.ConnectionFactoryType">topic</parameter>
   <parameter name="transport.oracleaq.Destination">OWNER.QUEUE_NAME</parameter>
   <description/>
</proxy>
```