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
 
#Programming Demo


1. Programming Demo

 1.1 Message Programming(MQ-based)
 
 Assuming that we are in Member signup condition, 
 
 we need to notify member when they signup successful by sms.
 
 Define a `MemberService` Class, Which injected `MessageSender`.
 
 Use `send` method to produce message.

 ``` java
 @Service
 public class MemberService {

    @Autowired 
    private MemberDao memberDao;
    
    // MesageSender is provided by Koper framework.
    // We can configure it with Spring by configuration File such as <bean>
    @Autowired
    private MessageSender messageSender;

    public void signup(Member member) {
        memberDao.createMember(member);
        final String topic = "com.zhaimi.message.demo.message.notifyMemberAfterSignup";
        // OLD Style: sync notify smsService.sendSms(topic, "You have signed up successfully!" + member.getPhoneNo());
        // New Style: async notify
        messageSender.send(topic, "You have signed up successfully! " + member.getPhoneNo()");
    }
    
 }
 ```

 Define a `MemberSignupListener` Class, Which extends `AbstractMessageListener` class.
 
 Use `@Listen` annotation to specify the exact `Topic` you want to listen.

 ``` java
 @Component
 public class MemberSignupListener extends AbstractMessageListener {

    @Autowired
    private SmsService smsService;

    @Listen(topic = "com.zhaimi.message.demo.message.notifyMemberAfterSignup")
    @Override
    public void onMessage(String msg) {
        smsService.sendSms(msg);
    }
    
 }
 ```

 1.2 Data Event Programming
 
 Koper will intercept method and take the parameters as message after calling it.
 
 The corresponding message topic is consist of `package name` + `class name` + `method name`.
 ``` java 
 package com.zhaimi.message.demo.dataevent.dao.impl;
 
 @Repository
 public class OrderDao implements OrderDaoImpl {

    @Autowired
    private SqlSessionTemplate sqlSessionTemplate;
    
    @Override
    public Integer insertOrder(Order order) {
        sqlSessionTemplate.insert("OrderDao.insert", order);
        return 1;
    }

    @Override
    public Integer updateOrder(Order order) {
        sqlSessionTemplate.update("OrderDao.update", order);
        return 1;
    }
    
 }
 ```
 
 On the other hand, define a Listener class, 
 
 use `@DataListener` annotation to specify `dataObject` attribute(the same to topic) you want to listen.
 ``` java
 @Component
 @DataListener(dataObject = "com.zhaimi.message.demo.dataevent.dao.impl.OrderMapperImpl")
 public class OrderListener {

    public void onInsertOrder(Order order) {
        System.out.println("orderNo : " + order.getOrderNo());
        System.out.println("create time : " + order.getCreatedTime());
        // do some data analysis
    }

    public void onUpdateOrder(Order order) {
        System.out.println("orderNo : " + order.getOrderNo());
        System.out.println("create time :" + order.getCreatedTime());
        // do some data analysis
    }
    
 }
 ```

# User Guide
1. Quick Start

2. Achitecture

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

