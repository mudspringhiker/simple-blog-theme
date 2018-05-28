---
layout: post
title: Event Emitters
tags: ["web_development", "JavaScript"]
---

I find the concept of event emitters a litle confusing, even though it sounds simple. A month after starting my web development bootcamp, I had a hard time applying this concept (or remembering how event emitters work) at the time I needed to apply it, specifically, in exploring WebSockets (a topic for another time).

Event emitters are objects having 'events' as keys and arrays of 'call back functions' as values for these event keys. For a newbie like me, this may look like the following:

```javascript
eventEmitter = {
    event1: [callBackFunc1, callBackFunc2, ...],
    event2: [callBackFunc5, callBackFunc3, ...],
    ...,
    ...
}
```

Aside from this, event emitters have prototypal methods, `on` and `emit`. The `on` method accepts two arguments:

- a string representing the event to listen for 
- a callback function to invoke when that event occurs

For example:

```javascript
ee.on('newMessage', (message) => console.log('Got message: ', message))
// ee = event emitter instance, from now on
```

The `emit` method, on the other hand, also takes in two methods (or one at least):

- a string representing the event to emit
- a `payload` that will be given to all callbacks registered for that event

For example:
```javascript
// First, a listener is registered for an event:
ee.on('newMessge', (message) => console.log('Got a message: ', message))

// When the emit method is called afterwards:
ee.emit('newMessage', 'Hello!')
// This makes the callback from the `.on` to be invoked, resulting in printing
// of the following in the console:
// 'Got a message: Hello!'
```

The following code describes the EventEmitter class:

```javascript
class EventEmitter {
    constructor () {
        this.callbacks = {};
    }

    on (event, callback) {
        if (!this.callbacks[event]) {
            this.callbacks[event] = [callback];
        } else {
            this.callbacks[event].push(callback);
        }
    }

    emit (event, payload) {
        let results;
        if (Object.keys(this.callbacks).includes(event)) {
            results = this.callbacks[event].map(func => func(payload));
        }
        return results;
    }
}
```

### Reference:

Fullstack Academy lab workshop on event emitters 
