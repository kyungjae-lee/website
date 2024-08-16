[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Operating Systems</a> > Mutual Exclusion - Monitors (Programming Language Support)

# Mutual Exclusion - Message Passing



## Introduction

* Semaphores and monitors are designed for systems that **share memory** between processes or threads.
* All of the processes/threads involved in synchronization can reference the same semaphore or monitor objects.



## Message Passing

* When processes need to synchronize WITHOUT shared memory (e.g., distributed systems), **message passing** may be used.

  e.g., **Mailbox** or **port** is a place for a message to be queued up at a receiver. This enables a receiver to have multiple targets for messages if desired.



## Message Passing Forms

Both the sender and receiver can be blocking or non-blocking. Three combinations are common, although any particular system will usually have only one or two combination implemented:

* **Blocking send, blocking receive**
  * Full synchronization between two entities
  * Both the sender and receiver are blocked until the message is delivered; this is sometimes referred to as a **rendezvous**. This combination allows for tight synchronization between processes.
* **Non-blocking send, blocking receive**
  * Partial synchronization
  * Although the sender may continue on, the receiver is blocked until the requested message arrives. This is probably the most useful combination. It allows a process to send one or more messages to a variety of destinations as quickly as possible. A process that must receive a message before it can do useful work needs to be blocked until such a message arrives. e.g., A server process that exists to provide a service or resource to other processes.
* **Non-blocking send, non-blocking receive**
  * Neither entity must wait
  * Used for information sharing but not always for synchronization






## References

Stallings, W. (2018). *Operating Systems: Internals and Design Principles* (9th ed.). Pearson Education, Inc.
