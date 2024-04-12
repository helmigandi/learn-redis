# PubSub

![PubSub](/images/redis-pubsub.png "PubSub")

- Used for application integration.
- Consumer/Subscriber: application that receive data.
- If there are no Consumer/Publisher, the data will be lost.
- It is not recommended if you want to save data.
- [PubSub documentation commands](https://redis.io/docs/latest/commands/?group=pubsub).

## Channel

![Channel](/images/channel.png "Channel")

- Key for send or receive data.
- Publisher: send data to channel.
- Subscriber: receive data from channel.
- Channel name must be unique.

## Subscribe

```bash
# subscribe to chat and notification channels
subscribe chat notification
# 1) "subscribe"
# 2) "chat"

# subscribe to chat channel
subscribe chat
# 1) "chat"

# exit from chat channel
unsubscribe chat
```

## Publisher

```bash
pubsub channels
# 1) "notification"
# 2) "chat"

publish chat "hello from redis pubsub"

publish notification "hello notification"
```

## Limitations

- Redis can not resend data to subscriber.
- Used Broadcast system, Redis can not filter the subscriber.
