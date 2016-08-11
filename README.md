# Koper
 Koper is a MQ-based and event-driven distributed programming framework.
 * Koper provides a set of simplified API to implement message programming.
 * Koper supports distributed listener model and data event model.
 
# Concept 
 * Core architecture:  Message Architeture， Event Driven Architecture（EDA）
 * Core concept:       message，producer, consumer，Message Queue(MQ), topic, subscribe
 * Core component:     MessageSender, MessageListener, DataEventListener.
 * High-level concept: consumer group, message partition


# Feature
 *  A simplified set of MQ-based programming model and API.
 *  A simplified set of data event API.
  * Easy to develop asynchronous process on data event.
 *  MQ provider independent. It supports Kafka, RabbitMQ or other provider as message queue.
 *  High performance and scalability.
 *  High avalibility.
 *  High extensibility.
 *  High-level feature, time spot recording, message tracking
 
# User Guide
1. Programming Demo

 1.1 Message Programming(MQ-based)
 
 Define a `MessageProducer` Class, Which injected `MessageSender`.
 
 Use `send` method to produce msg.

 ``` java
 @Component
 public class MsgProducer {

    // MesageSender is provided by Koper framework.
    // We can configure it with Spring by configuration File such as <bean>
    @Autowired
    private MessageSender messageSender;

    public void produceMsg() {
        final String topic = "com.zhaimi.message.demo.message.refreshCache";
        final String cacheKey = "some/building";
        messageSender.send(topic, cacheKey);
    }
    
 }
 ```

 Define a `MessageConsumer` Class, Which extends `AbstractMessageListener` class.
 
 Use `@Listen` annotation to specify the exact `Topic` you want to listen.

 ``` java
 @Component
 public class MsgConsumer extends AbstractMessageListener {

    @Listen(topic = "com.zhaimi.message.demo.message.refreshCache")
    @Override
    public void onMessage(String msg) {
        System.out.println("current time is " + LocalDateTime.now().toString() + ", " +
                "MsgConsumer receive msg, msg is " + msg);
    }
    
 }
 ```

 1.2 Data Event Programming
 
 Koper will intercept method and take the parameters as message after calling it.
 
 The corresponding message topic is consist of `package name` + `class name` + `method name`.
 ``` java 
 package com.zhaimi.message.demo.dataevent.mapper.impl;
 
 @Repository
 public class OrderMapperImpl implements OrderMapper {

    @Override
    public Integer insertOrder(Order order) {
        System.out.println("orderNo : " + order.getOrderNo());
        System.out.println("create time: " + order.getCreatedTime());
        return 1;

    }

    @Override
    public Integer updateOrder(Order order) {
        System.out.println("orderNo: " + order.getOrderNo());
        System.out.println("create time: " + order.getCreatedTime());
        return 1;
    }
    
 }
 ```
 
 On the other hand, define a Listener class, 
 
 use `@DataListener` annotation to specify `dataObject` attribute(the same to topic) you want to listen.
 ``` java
 @Component
 @DataListener(dataObject = "com.zhaimi.message.demo.dataevent.mapper.impl.OrderMapperImpl")
 public class OrderListener {

    public void onInsertOrder(Order order) {
        System.out.println("orderNo : " + order.getOrderNo());
        System.out.println("create time : " + order.getCreatedTime());
    }

    public void onUpdateOrder(Order order) {
        System.out.println("orderNo : " + order.getOrderNo());
        System.out.println("create time :" + order.getCreatedTime());
    }
    
 }
 ```

2. Quick Start

3. Achitecture

4. Message Programming(MQ-based)
   
 Listener Model

5. Data Event Programming

 Data Listener Model

6. Performance and Scalability

7. High avalibility
8. High extensibility.
9. High-level feature

 9.1 Time spot recording
 
 9.2 Message tracking

