---
layout: post
title: "Dropwizard, Hibernate and, Session Issue"
excerpt: "How to solve hibernate's 'No session currently bound to execution context' when using objects that are not managed by dropwizard."
date: 2016-05-13
tags: [Dropwizard, Hibernate, Session]
author: ggarciajr
comments: true
---

Today I got myself into trouble when trying to use a DAO object inside of an event handler that was listening to a SQS queue.

Whenever my handler tried to consume a new message from the queue I got this **'No session currently bound to execution context'** exception.

My handler was registered in the SQSListener which is provided by the <a href="https://github.com/bascan/sqs-dropwizard" target="_blank" rel="noopener noreferrer">sqs-dropwizard</a> plugin.

The problem here is that the Handler is not managed by <a href="http://www.dropwizard.io/0.9.2/docs/" target="_blank" rel="noopener noreferrer">Dropwizard</a>, what causes the hibernate session not to be bound to any context.

To solve the problem and get all the dropwizard magic to work inside the handler I had to manually bind the session to a **ManagedSessionContext**.

The idea is to create a method that binds a new session to the ManagedSessionContext and that takes a consumer  function that will execute the event handling logic.

```java
import com.amazonaws.services.sqs.model.Message;
import org.hibernate.CacheMode;
import org.hibernate.FlushMode;
import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.context.internal.ManagedSessionContext;

import java.util.function.Consumer;

interface EventHandler {
    /* Necessary to wrap event handlers that needs to access the DB.         */
    /* EventHandlers are not dropwizard managed objects and thus they don't  */
    /* get a hibernate session bound to the session context.                 */
    /* This causes any attempt to use a DAO object to fail and that is why   */
    /* we need to manually bind a new session to a ManagedSessionContext so  */
    /* we can get Dropwizard magic on event handlers                         */
    default void executeWithSession(
      SessionFactory sessionFactory,
      Message message,
      Consumer<Message> consumer
    ) {
        Session session = sessionFactory.openSession();
        session.setDefaultReadOnly(true);
        session.setCacheMode(CacheMode.NORMAL);
        session.setFlushMode(FlushMode.MANUAL);
        ManagedSessionContext.bind(session);
        // DropWizard magic enabled from this point.

        consumer.accept(message);

        session.close();
        // DropWizard magic disabled from this point.
    }
}
```

And then, in your handler, you just have to wrap the code that consumes messages from the queue in a call to the **executeWithSession** method.

```java
@Override
public void handle(Message message) {
  executeWithSession(sessionFactory, message, (msg) -> {
    //do whatever you need with the message in here
  });
}
```
