[Home](../../) | [Projects](../../projects) | [Notes](../)k</a> > <a href="./">Multi-Threading (POSIX Threads)</a> > Notification Chains (NFC)

# Notification Chains (NFC)



## Introduction to Notification Chains

* Notification Chain is an architectural concept (i.e., can be implemented in many other programming languages) used to notify multiple subscribers that are interested in a particular event. Basically, it is all about the communication between a publisher and the multiple subscribers.
* A party which generates an event is called the **Publisher**, and the parties that are interested in being notified of the event are called **Subscribers**.

* There is one publisher and multiple subscribers.
* Once an event is generated/produced by the publisher, the event is pushed to the subscribers that are interested in it.
* Subscribers can register and de-register for events at their will.

* Publishers/subscribers could be any entities:

  * Multiple threads of the same process

  * Multiple processes running on the same system

  * Multiple processes running on the different systems

  * Different components of a big software system



## NFC - Data Structure

* NFC is a linked list of callbacks (function pointers).

  ```c
  /* structure for NFC */
  typedef struct notif_chain_
  {
      char nfc_name[64];				/* optional */
      glthread_t notif_chain_head;	/* head of the linked list */
  } notif_chain_t;
  ```

* Each element in NFC is called the **notification chain element**.

  ```c
  /* structure for notification chain elements */
  typedef struct notif_chain_elem_
  {
      char key[MAX_NOTIV_KEY_SIZE];	/* 64 or 128 bytes will be sufficient */
      size_t key_size;
      bool_t is_key_set;	/* user specified the key or not? (whild card user may not) */
      nfc_app_cb app_cb;	/* function pointer by which subscribers want to get notified */
      glthread_t glue;
  } notif_chain_elem_t;
  
  /* prototype of the function pointer must be generic so that it works for all types of data for all types of subscribers */
  typedef void (*nfc_app_cb)(void *, size_t);	/* 2nd arg: size of the 1st arg */
  ```



## NFC - Implementation

The following example of NFC implementation is completely generic and can be applied to any type of applications.

* notif.h

  ```c
  /*
   * File Name    : notif.h
   * Description  : Interface for generic notification chain (NFC) infrastructure
   * Author       : Comments added by Kyungjae Lee (Original: Abhishek Sagar)
   * Date Created : 12/29/2022
   */
  
  #ifndef NOTIF_CHAIN
  #define NOTIF_CHAIN
  
  #include <stddef.h>	/* size_t */
  #include "utils.h"
  #include "gluethread/glthread.h"
  
  #define MAX_NOTIF_KEY_SIZE 64
  
  typedef void (*nfc_app_cb)(void *, size_t);
  
  typedef struct notif_chain_elem_
  {
      char key[MAX_NOTIV_KEY_SIZE];	/* 64 or 128 bytes will be sufficient */
      size_t key_size;
      bool_t is_key_set;	/* user specified the key or not? (whild card user may not) */
      nfc_app_cb app_cb;	/* function pointer by which subscribers want to get notified */
      glthread_t glue;
  } notif_chain_elem_t;
  
  typedef struct notif_chain_
  {
      char nfc_name[64];				/* optional */
      glthread_t notif_chain_head;	/* head of the linked list */
  } notif_chain_t;
  
  void nfc_register_notif_chain(notif_chain_t *nfc, notif_chain_elem_t *nfce);
  void nfc_invoke_notif_chain(notif_chain_t *nfc, 
                              void *arg, size_t arg_size, 
                              char *key, size_t key_size);
  
  #endif /* NOTIF_CHAIN */
  ```

* notif.c

  ```c
  /*
   * File Name    : notif.c
   * Description  : Implementation of generic notification chain (NFC) infrastructure
   * Author       : Comments added by Kyungjae Lee (Original: Abhishek Sagar)
   * Date Created : 12/29/2022
   */
  
  #include <stdlib.h>
  #include <memory.h>
  #include <assert.h>
  #include "notif.h"
  
  void nfc_register_notif_chain(notif_chain_t *nfc, notif_chain_elem_t *nfce)
  {
      /* subscriber already filled up the element to insert. just make a copy */
      notif_chain_elem_t *new_nfce = calloc(1, sizeof(notif_chain_elem_t));
      memcpy(new_nfce, nfce, sizeof(notif_chain_elem_t));
      
      /* insert the new node into NFC */
      glthread_add_next(&nfc->notif_chain_head, &new_nfc->glue);
      
  }
  
  /* function to be invoked by the publisher to notify the subscriber */
  /* publisher will traverse the entire linked list passed as the 1st arg, match each 
     notification element with the key, and if there's a match, the callback registered
     to that element will be invoked as a means of notifying the subscriber */
  void nfc_invoke_notif_chain(notif_chain_t *nfc, 
                              void *arg, size_t arg_size, 
                              char *key, size_t key_size)
  {
      /* iterate over the NFC elements */
      glthread_t *curr;
      notif_chain_elem_t *nfce;
      assert(key_size <= MAX_NOTIF_KEY_SIZE);
      
      ITERATE_GLTHREAD_BEGIN(&nfc->notif_chain_head, curr)
      {
          /* pick up the NFC element one by one */
       	nfce = glthread_glue_to_notif_chain_elem(curr);
          
          /* handle the wild card NFC element */
          if(!(key && key_size && nfce->is_key_set && (key_size == nfce->key_size)))
          {
              /* if the key is not specified OR if the key in the NFC element is not specified
                 (which essentially means that it is a wild card entry) 
                 then, no matter what, the publisher should invoke the callback present in the
                 NFC element */
              nfce->app_cb(arg, arg_size);
              
              /* note that the publisher uses 'arg' and 'arg_size' passed by the subscriber when
                 notifying the subscriber */
          }
          /* handle the situation where the key has been specified */
          else
          {
              if (memcmp(key, nfce->key, key_size) == 0)
              {
                  /* invoke the callback only when the passed key matches the key in the NFC
                     element */
                  nfce->app_cb(arg, arg_size);
              }
          }
      }ITERATE_GLTHREAD_BEGIN(&nfc->notif_chain_head, curr);
  }
  ```
  

You are free to implement any additional routines. e.g., Unregister or unsubscription function using which a subscriber can unsubscribe from the notification chain.





## References

Sagar, A. (2022). *Part A - Multithreading & Thread Synchronization - Pthreads* [Video file]. Retrieved from  https://www.udemy.com/course/multithreading_parta/
