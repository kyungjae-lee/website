[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Multi-Threading (POSIX Threads)</a> > Thread Synchronization - Thread-Safe Highly Concurrent CRUD Operations - Demonstration

# Thread Synchronization - Thread-Safe Highly Concurrent CRUD Operations - Demonstration



This section demonstrates a 100% thread-safe, accurate, highly-concurrent, and deadlock free application whose state is guaranteed to be consistent at any given point in time.



## Demo Project Setup

* Create a folder containing the following header & source files:

  * `crud_algorithm.c` - CRUD algorithm (main)
  * `linked_list.h`, `linked_list.c` - Linked list data structure
  * `ref_count.h`, `ref_count.c` - Reference count data structure
  * `student_list.h`, `student_list.c` - Student list data structure

* Build using:

  ```plain
  gcc *.c -lpthread
  ```

* Run using:

  ```plain
  ./a.out
  ```

* To analyze the outputs using the log file:

  ```plain
  ./a.out > log
  ```

  > Open `log`.
  >
  > Find deferred deletion (`/deferred`) - Let's say we found `DELETE TH :: Roll No 9 deletion deferred`.
  >
  > Extract info we are interested in (`grep "Roll No 9" log > rollno9.txt`)
  >
  > 
  >
  > How to check if the program is deadlock free? $\to$ Inspect the change in `loop_count` variable.
  >
  > 1. Run the program in GDB for a couple of seconds using `gdb a.out` $\to$ `run` .
  >
  > 2. `Ctrl+c` to stop the program.
  >
  > 3. Print the loop count using `print loop_count` or `p loop_count` and save it.
  >
  > 4. Continue running the program by using `continue` or `c`.
  >
  > 5. `Ctrl+c` to stop the program again.
  >
  > 6. Check the value of `loop_count` and compare it with the previously saved value.
  >
  >    If each element of `loop_count` has increased, then we can conclude that there's no deadlock.
  >
  > 7. Repeat 4~6 and monitor if there's any element that has not changed at all. If there an element that's not changing forever, we can conclude that the corresponding thread is situated in a deadlock.



## Demo Project and Deadlocks

* Is our demo project deadlock-free?

  * **Yes**, provided that a thread holds lock on only one student object at any given point in time.

  * **No**, deadlock may occur if a thread works with 2 or more student objects at any given point in time.

    In this case, **Deadlock Detection Algorithms** must be deployed as a part of your project to guarantee deadlock-free execution.

  Since we have designed the program in such a way that no thread holds 2 or more student objects at the same time, deadlock will NOT occur. 

* CRUD operations usually involve a thread performing operations only on 1 object at a time. If a thread need to update more than 1 objects, it should update them one by one. (Do not hold write lock on more than 1 object at a time.)

* These CRUD algorithms are being used in industry gracefully without assistance of any **Deadlock Detection Algorithms**.



## Demo Source Code

* **Implementation of CRUD Algorithm**

  ```c
  /*
   * File Name    : crud_algorithm.c
   * Description  : Implementation of CRUD algorithm
   * Author       : Modified by by Kyungjae Lee (Original: Abhishek Sagar)
   * Date Created : 01/15/2023
   */
  
  #include <stdio.h>
  #include <stdlib.h>
  #include <assert.h>
  #include "student_list.h"
  
  #if 0
  
   Signs that this program is erroneous :
   1. Any threads goes in Deadlock ( you will cease to see output from 1 or more threads )
   2. Marks of student update by more than INCR
   3. Any crash
   4. New Read/Write Operation is performed on a student whose deletion is deferred
   5. Student after "delete deferred" , not actually deleted
   6. Memory Leak ( of student objects )
   7. Ref Count value of student object goes too high ( more than 4 )
  
  #endif
  
  #define MAX_ROLL_NO 10
  #define INCR 5
  
  enum {
      READER_TH,
      UPDATE_TH,
      CREATE_TH,
      DELETE_TH,
      THREAD_TYPE_MAX
  } th_type_t;
  
  static pthread_t threads[THREAD_TYPE_MAX];
  static uint32_t loop_count[THREAD_TYPE_MAX];
  
  static stud_lst_t stud_lst; /* Container Object */
  
  static void* reader_fn (void *arg)
  {
      student_t *stud;
      uint32_t roll_no;
  
      while(1)
      {
          loop_count[READER_TH]++;
  
          roll_no = rand() % MAX_ROLL_NO; /* now, reader thread knows which student it needs
          								   to perform a read operation on */
          pthread_rwlock_rdlock(&stud_lst.rw_lock);
  
          stud = student_lst_lookup(&stud_lst, roll_no);
  
          if (!stud)
          {
              printf("READ TH  ::  Roll No %u Do not Exist\n", roll_no);
              pthread_rwlock_unlock(&stud_lst.rw_lock);
              continue;
          }
  
          /* current thread got an access to object */
          thread_using_object(&stud->ref_count);
  
          /* prepare to perform READ operation on student object */
          pthread_rwlock_rdlock(&stud->rw_lock);
  
          /* current thread has done all pre-requisites to perform read operation on 
             the student object, unlock the container level lock */
          pthread_rwlock_unlock(&stud_lst.rw_lock);
  
          /* now perform the READ operation */
          printf("READ TH  ::  Roll No %u is READ, total marks = %u\n", 
                  roll_no, stud->total_marks);
  
          /* unlock the Student object */
          pthread_rwlock_unlock(&stud->rw_lock);
  
          /* done using the object */
          if (thread_using_object_done (&stud->ref_count))
          {
              printf("READ TH  ::  Roll No %u READ Done\n",  roll_no);
              student_destroy (stud);
              printf("READ TH :: Roll No %u destroyed\n", roll_no);
              continue;
          }
          else 
          {
              /* Never attempt to access stud object here or after this line in program*/
              printf ("READ TH  ::  Roll No %u READ Done\n", roll_no);
          }
      }
  
      return NULL;
  }
  
  static void* update_fn (void *arg)
  {
      student_t *stud;
      uint32_t roll_no;
  
      while(1)
      {
          loop_count[UPDATE_TH]++;
          
          roll_no = rand() % MAX_ROLL_NO; /* now, writer thread knows which student it needs
          								   to perform a read operation on */
          pthread_rwlock_rdlock (&stud_lst.rw_lock);
  
          stud = student_lst_lookup (&stud_lst, roll_no);
  
          if (!stud)
          {
              printf ("UPDATE TH  ::  Roll No %u Do not Exist\n", roll_no);
              pthread_rwlock_unlock (&stud_lst.rw_lock);
              continue;
          }
  
          /* current thread got an access to object */
          thread_using_object(&stud->ref_count);
  
          /* prepare to perform WRITE Operation on student object */
          pthread_rwlock_wrlock(&stud->rw_lock);
  
          /* current thread has done all pre-requisites to perform write operation on 
             the student object, unlock the container level lock */
          pthread_rwlock_unlock(&stud_lst.rw_lock);
  
          /* now perform UPDATE operation */
          uint32_t old_marks = stud->total_marks;
          stud->total_marks = stud->total_marks + INCR;
          stud->total_marks = stud->total_marks % 100;
  
          printf ("UPDATE TH  ::  Roll No %u , Increment %d, Marks Update %u --> %u\n",
              stud->roll_no, INCR, old_marks, stud->total_marks);
  
          /* unlock the Student Object */
          pthread_rwlock_unlock(&stud->rw_lock);
  
          /* done using the object */
          if (thread_using_object_done (&stud->ref_count))
          {
              printf ("UPDATE TH  ::  Roll No %u UPDATE Done\n",  roll_no);
              student_destroy (stud);
              printf ("UPDATE TH :: Roll No %u destroyed\n", roll_no);
              continue;
          }
          else
          {
              /* never attempt to access stud object here or after this line in program*/
              printf ("UPDATE TH  ::  Roll No %u UPDATE Done\n",  roll_no);
          }
      }
  
      return NULL;
  }
  
  static void* create_fn (void *arg)
  {
      uint32_t roll_no;
      student_t *new_stud;
  
      while (1)
      {
          loop_count[CREATE_TH]++;
  
          roll_no = rand() % MAX_ROLL_NO;
  
          pthread_rwlock_wrlock (&stud_lst.rw_lock);
  
          new_stud = student_lst_lookup(&stud_lst, roll_no);
  
          if (new_stud)
          {
              printf ("CREATE TH :: Roll No %u CREATION Failed, Already Exist\n", roll_no);
              pthread_rwlock_unlock (&stud_lst.rw_lock);
              continue;
          }
  
          new_stud = student_malloc(roll_no);
          assert(student_lst_insert(&stud_lst, new_stud));
          ref_count_inc(&new_stud->ref_count);
          printf ("CREATE TH :: Roll No %u CREATION Success\n", new_stud->roll_no);
  
          pthread_rwlock_unlock (&stud_lst.rw_lock);
      }
  
      return NULL;
  }
  
  static void* delete_fn (void *arg)
  {
      uint32_t roll_no;
      student_t *stud;
  
      while (1)
      {
          loop_count[DELETE_TH]++;
  
          roll_no = rand() % MAX_ROLL_NO;
  
          pthread_rwlock_wrlock (&stud_lst.rw_lock);
  
  		stud = student_lst_remove (&stud_lst, roll_no);
  
          if (!stud)
          {
        		printf("DELETE TH :: Roll No %u DELETION Failed, Do not Exist\n", roll_no);
           	pthread_rwlock_unlock(&stud_lst.rw_lock);
             	continue;
         	}
  
          /* switching the order of the following two statements will results in crash */
          thread_using_object(&stud->ref_count);
          assert(!ref_count_dec(&stud->ref_count));
  
          pthread_rwlock_unlock(&stud_lst.rw_lock);
  
          printf("DELETE TH :: Roll No %u Removal Success\n", roll_no);
  
          /* done using the object */
          if (thread_using_object_done (&stud->ref_count))
          {
              student_destroy (stud);
              printf ("DELETE TH :: Roll No %u destroyed\n", roll_no);
          }
          else
          {
              /* delete thread cannot delete the object since it is currently being used by 
                 another thread (in this case, the thread currently using the object will
                 delete it when done with the currently on-going operation) */
              printf ("DELETE TH :: Roll No %u deletion deferred\n", roll_no);
          }
  
      }
  
      return NULL;
  }
  
  int main (int argc, char *argv[])
  {
      stud_lst.lst = init_singly_ll();
      pthread_rwlock_init(&stud_lst.rw_lock, NULL);
  
      loop_count[READER_TH] = 0;
      loop_count[UPDATE_TH] = 0;
      loop_count[CREATE_TH] = 0;
      loop_count[DELETE_TH] = 0;
  
      pthread_attr_t attr;
      pthread_attr_init(&attr);
      pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_DETACHED);
  
      pthread_create (&threads[READER_TH], &attr, reader_fn, NULL);
      pthread_create (&threads[UPDATE_TH], &attr, update_fn, NULL);
      pthread_create (&threads[CREATE_TH], &attr, create_fn, NULL);
      pthread_create (&threads[DELETE_TH], &attr, delete_fn, NULL);
  
      pthread_exit(0); /* so that the termination of the main thread does not force the
      	termination of the spawned threads */
      return 0;
  }
  ```

  If in the program, you could find any sequence of instructions that will lead to an inconsistent state (e.g., update thread still accessing the element which has already been destroyed by delete thread) of the system,  then your CRUD algorithm is INACCURATE! By coming up with different possible scenarios (sequence of execution) and keeping track of the **reference count**, you will be able to identify possible inconsistencies. 

  Sequence diagrams help analyzing the flow of the threads. 

  See [Thread Synchronization - Thread-Safe Highly Concurrent CRUD Operations](./thread-synchronization-thread-safe-highly-concurrent-crud-operations) for the sequence diagrams.

* **Interface for Reference Count Data Structure**

  ```c
  /*
   * File Name    : ref_count.h
   * Description  : Interface for reference count data structure
   * Author       : Modified by by Kyungjae Lee (Original: Abhishek Sagar)
   * Date Created : 01/15/2023
   */
  
  #ifndef REF_COUNT_H
  #define REF_COUNT_H
  
  #include <pthread.h>
  #include <stdint.h>
  #include <stdbool.h>
  
  typedef struct ref_count_
  {
      uint32_t ref_count;				/* can't go negative */
      pthread_spinlock_t spinlock;	/* to support provide mutual exclusion */
  } ref_count_t;
  
  void ref_count_init(ref_count_t *ref_count);
  void ref_count_inc(ref_count_t *ref_count);
  bool ref_count_dec(ref_count_t *ref_count); /* returns true if ref_count after dec is zero*/
  void ref_count_destroy(ref_count_t *ref_count);
  void thread_using_object(ref_count_t *ref_count);
  bool thread_using_object_done(ref_count_t *ref_count);
  
  #endif /* REF_COUNT_H */
  ```

* **Implementation of Reference Count Data Structure**

  ```c
  /*
   * File Name    : ref_count.c
   * Description  : Implementation of reference count data structure
   * Author       : Modified by by Kyungjae Lee (Original: Abhishek Sagar)
   * Date Created : 01/15/2023
   */
  
  #include <assert.h>
  #include "ref_count.h"
  
  void ref_count_init(ref_count_t *ref_count)
  {
      /* initialize members */
      ref_count->ref_count = 0;
      pthread_spin_init(&ref_count->spinlock, PTHREAD_PROCESS_PRIVATE);
  }
  
  void ref_count_inc(ref_count_t *ref_count)
  {
      pthread_spin_lock(&ref_count->spinlock);
      ref_count->ref_count++;
      pthread_spin_unlock(&ref_count->spinlock);
  }
  
  /* returns true if ref_count after decrement is zero*/
  bool ref_count_dec(ref_count_t *ref_count)
  {
      bool rc;
      pthread_spin_lock(&ref_count->spinlock);
      assert(ref_count->ref_count); /* ensure that ref_count never goes below zero */
      ref_count->ref_count--;
      rc = (ref_count->ref_count == 0) ? true : false; 
      pthread_spin_unlock(&ref_count->spinlock);
      return rc;
  }
  
  void ref_count_destroy(ref_count_t *ref_count)
  {
      assert(ref_count->ref_count == 0);
      pthread_spin_destroy(&ref_count->spinlock);
  }
  
  void thread_using_object(ref_count_t *ref_count)
  {
      ref_count_inc(ref_count);
  }
  
  bool thread_using_object_done(ref_count_t *ref_count)
  {
      return ref_count_dec(ref_count);
  }
  ```

  > Basically, the value of `ref_count` refers to the number of entities in the system that are referencing (or using) that reference count object.

* **Interface for Student List**

  ```c
  /*
   * File Name    : student_list.h
   * Description  : Interface for student list
   * Author       : Modified by by Kyungjae Lee (Original: Abhishek Sagar)
   * Date Created : 01/15/2023
   */
  
  #ifndef STUDENT_LIST_H
  #define STUDENT_LIST_H
  
  #include <stdint.h>
  #include <pthread.h>
  #include "ref_count.h"
  #include "linked_list.h"
  
  typedef struct student_ 
  {
      uint32_t roll_no;
      uint32_t total_marks;
      ref_count_t ref_count;
      pthread_rwlock_t rw_lock;
  } student_t;
  
  typedef struct stud_lst_
  {
      ll_t *lst;
      pthread_rwlock_t rw_lock;
  } stud_lst_t;
  
  student_t* student_malloc(uint32_t roll_no);
  void student_destroy(student_t *stud);
  student_t* student_lst_lookup(stud_lst_t *stud_lst, uint32_t roll_no);
  bool student_lst_insert(stud_lst_t *stud_lst, student_t *stud);
  student_t* student_lst_remove(stud_lst_t *stud_lst, uint32_t roll_no);
  
  #endif /* STUDENT_LIST_H */
  ```

* **Implementation of Student List**

  ```c
  /*
   * File Name    : student_list.c
   * Description  : Implementation of student list
   * Author       : Modified by by Kyungjae Lee (Original: Abhishek Sagar)
   * Date Created : 01/15/2023
   */
  
  #include <stdlib.h>
  #include <assert.h>
  #include "student_list.h"
  
  static uint32_t student_object_count = 0;
  
  student_t* student_malloc(uint32_t roll_no)
  {
      student_t *new_stud = (student_t *)calloc(1, sizeof(student_t));
      new_stud->roll_no = roll_no;
      new_stud->total_marks = 0;
      ref_count_init(&new_stud->ref_count);
      pthread_rwlock_init(&new_stud->rw_lock, NULL);
      student_object_count++;
      return new_stud;
  }
  
  void student_destroy(student_t *stud)
  {
      assert(stud->ref_count.ref_count == 0);
      /* free stud resources */
      ref_count_destroy(&stud->ref_count);
      pthread_rwlock_destroy(&stud->rw_lock);
      free(stud);
      student_object_count--;
  }
  
  student_t* student_lst_lookup(stud_lst_t *stud_lst, uint32_t roll_no)
  {
      student_t *stud;
      singly_ll_node_t *node1, *node2;
  
      ITERATE_LIST_BEGIN2(stud_lst->lst, node1, node2)
      {
          stud = (student_t *)(node1->data);
          if (stud->roll_no == roll_no) return stud;
  
      } ITERATE_LIST_END2(stud_lst->lst, node1, node2) ;
  
      return NULL;
  }
  
  bool student_lst_insert(stud_lst_t *stud_lst, student_t *stud)
  {
      student_t *stud2 = student_lst_lookup(stud_lst, stud->roll_no);
      if (stud2) 
      {
          return false;
      }
  
      singly_ll_add_node_by_val(stud_lst->lst, stud);
      return true;
  }
  
  /* detaches an element from the list (it is the responsibility of the caller to free
     this element) */
  student_t* student_lst_remove(stud_lst_t *stud_lst, uint32_t roll_no)
  {
      student_t *stud = student_lst_lookup(stud_lst, roll_no);
  
      if (!stud)
      {
          return NULL;
      }
  
      singly_ll_delete_node_by_data_ptr (stud_lst->lst, stud);
      return stud;
  }
  ```


* **Interface for Linked List**

  ```c
  /*
   * File Name    : linked_list.h
   * Description  : Interface for linked list
   * Author       : Modified by by Kyungjae Lee (Original: Abhishek Sagar)
   * Date Created : 01/15/2023
   */
  
  #ifndef LINKED_LIST_H
  #define LINKED_LIST_H
  
  #define GET_HEAD_SINGLY_LL(ll) (ll->head)
  #define INC_NODE_COUNT_SINGLY_LL(ll) (ll->node_count++)
  #define DEC_NODE_COUNT_SINGLY_LL(ll) (ll->node_count--)
  #define GET_NODE_COUNT_SINGLY_LL(ll) (ll->node_count)
  #define GET_NEXT_NODE_SINGLY_LL(node) (node->next)
  
  typedef enum {
      LL_FALSE,
      LL_TRUE
  } bool_t;
  
  typedef struct LL_Node
  {
      void *data;
      struct LL_Node *next;
  } singly_ll_node_t;
  
  typedef struct LL
  {
      unsigned int node_count;
      singly_ll_node_t *head;
      int (*comparison_fn)(void*, void *);
      int (*order_comparison_fn)(void *, void *);
  } ll_t;
  
  ll_t* init_singly_ll();
  singly_ll_node_t* singly_ll_init_node(void* data);
  int singly_ll_add_node(ll_t *ll, singly_ll_node_t *node);
  int singly_ll_add_node_by_val(ll_t *ll, void* data);
  int singly_ll_remove_node(ll_t *ll, singly_ll_node_t *node);
  unsigned int singly_ll_remove_node_by_value(ll_t *ll, void* data, int size);
  bool_t is_singly_ll_empty(ll_t *ll);
  void print_singly_LL(ll_t *ll);
  void reverse_singly_ll(ll_t *ll);
  void delete_singly_ll(ll_t *ll);
  int singly_ll_delete_node(ll_t *ll, singly_ll_node_t *node);
  unsigned int singly_ll_delete_node_by_value(ll_t *ll, void *data, int size);
  singly_ll_node_t *singly_ll_get_node_by_data_ptr(ll_t *ll, void *data);
  void singly_ll_delete_node_by_data_ptr(ll_t *ll, void *data);
  unsigned int singly_ll_remove_node_by_dataptr(ll_t *ll, void *data);
  void singly_ll_set_comparison_fn(ll_t *ll, int (*comparison_fn)(void *, void *));
  void singly_ll_set_order_comparison_fn(ll_t *ll, int (*order_comparison_fn)(void *, void *));
  void * singly_ll_search_by_key(ll_t *ll, void *key);
  void copy_singly_ll(ll_t *src, ll_t *dst);
  ll_t * union_singly_ll(ll_t *list1, ll_t *list2);
  void singly_ll_delete_data_by_key(ll_t *list, void *key);
  void singly_ll_add_ordered_data(ll_t *ll, void *data);
  
  #define ITERATE_LIST_BEGIN(list_ptr, node_ptr)                              \
      {                                                                       \
      singly_ll_node_t *_node_ptr = NULL;                                     \
      node_ptr = GET_HEAD_SINGLY_LL(list_ptr);                                \
      for(; node_ptr!= NULL; node_ptr = _node_ptr){                           \
          _node_ptr = node_ptr->next; 
  
  #define ITERATE_LIST_END  }} 
  
  /* delete safe loop*/
  #define ITERATE_LIST_BEGIN2(list_ptr, node_ptr, prev)                       \
      {                                                                       \
      singly_ll_node_t *_node_ptr = NULL; prev = NULL;                        \
      node_ptr = GET_HEAD_SINGLY_LL(list_ptr);                                \
      for(; node_ptr!= NULL; node_ptr = _node_ptr){                           \
          _node_ptr = node_ptr->next; 
           
  #define ITERATE_LIST_CONTINUE2(list_ptr, node_ptr, prev)    \
           {if(node_ptr) prev = node_ptr;                     \
           continue;}
              
  #define ITERATE_LIST_BREAK2(list_ptr, node_ptr, prev)       \
           break
  
  #define ITERATIVE_LIST_NODE_DELETE2(list_ptr, node_ptr, prev)   \
          {if(node_ptr && prev == NULL){                          \
              list_ptr->head = node_ptr->next;                    \
          }                                                       \
          else if(node_ptr && prev){                              \
              prev->next = node_ptr->next;                        \
          }                                                       \
          XFREE(node_ptr);                                        \
          list_ptr->node_count--;                                 \
          node_ptr = NULL;}
              
  #define ITERATE_LIST_END2(list_ptr, node_ptr, prev)   \
               if(node_ptr) prev = node_ptr; }} 
  
  #define LL_LESS_THAN(listptr, data1ptr, data2ptr)      \
      (listptr->order_comparison_fn(data1ptr, data2ptr) == -1)
  
  #define LL_GREATER_THAN(listptr, data1ptr, data2ptr)   \
      (listptr->order_comparison_fn(data1ptr, data2ptr) == 1)
  
  #define LL_EQUAL(listptr, data1ptr, data2ptr)          \
      (listptr->order_comparison_fn(data1ptr, data2ptr) == 0)
  
  #endif /* LINKED_LIST_H */
  ```

* **Implementation of Linked List**

  ```c
  /*
   * File Name    : linked_list.c
   * Description  : Implementation of linked list
   * Author       : Modified by by Kyungjae Lee (Original: Abhishek Sagar)
   * Date Created : 01/15/2023
   */
  
  #include <stdio.h>
  #include <stdlib.h>
  #include <memory.h>
  #include "linked_list.h"
  #include <assert.h>
  
  ll_t* init_singly_ll()
  {
      return calloc(1, sizeof(ll_t));
  }
  
  singly_ll_node_t* singly_ll_init_node(void* data)
  {
      singly_ll_node_t* node = calloc(1,  sizeof(singly_ll_node_t));
      node->data = data;
      return node;
  }
  
  void copy_singly_ll(ll_t *src, ll_t *dst)
  {    
      singly_ll_node_t* node = NULL;
  
      if (!src || !dst)
          return;
  
      delete_singly_ll(dst);
      ITERATE_LIST_BEGIN(src, node)
      {
          if(!node->data) 
              continue;
          singly_ll_add_node_by_val(dst, node->data);
      } ITERATE_LIST_END;
      singly_ll_set_comparison_fn(dst, src->comparison_fn);
  }
  
  
  ll_t* union_singly_ll(ll_t *list1, ll_t *list2)
  {    
      singly_ll_node_t* node = NULL;
  
      if (!list1)
          return list2;
      if (!list2)
          return list1;
  
      ll_t *res = init_singly_ll();
      singly_ll_set_comparison_fn(res, list1->comparison_fn ? list1->comparison_fn : list2->comparison_fn);
  
      ITERATE_LIST_BEGIN(list1, node)
      {
          singly_ll_add_node_by_val(res, node->data);
      } ITERATE_LIST_END;
      
      ITERATE_LIST_BEGIN(list2, node)
      {
          singly_ll_add_node_by_val(res, node->data);
      } ITERATE_LIST_END;
      return res;
  }
  
  int singly_ll_add_node(ll_t* ll, singly_ll_node_t *node)
  {
      if (!ll) return -1;
      if (!node) return -1;
      if (!GET_HEAD_SINGLY_LL(ll))
      {
          GET_HEAD_SINGLY_LL(ll) = node;
          INC_NODE_COUNT_SINGLY_LL(ll);
          return 0;
      }
  
      node->next = GET_HEAD_SINGLY_LL(ll);
      GET_HEAD_SINGLY_LL(ll) = node;
      INC_NODE_COUNT_SINGLY_LL(ll);
      return 0;
  }
  
  /* Duplicates will not be added*/
  int singly_ll_add_node_by_val(ll_t *ll, void *data)
  {
      if (singly_ll_get_node_by_data_ptr(ll, data))
          return -1;
      singly_ll_node_t* node = singly_ll_init_node(data);
      return singly_ll_add_node(ll, node);
  }
  
  int singly_ll_delete_node(ll_t *ll, singly_ll_node_t *node)
  {
      if (!ll) return -1;
      if (!GET_HEAD_SINGLY_LL(ll) || !node) return 0;
      singly_ll_node_t *trav = NULL;
      /*if node is not the last node*/
      if (node->next)
      {
          singly_ll_node_t *temp = NULL;
          node->data = node->next->data;
          temp = node->next;
          node->next = node->next->next;
          free(temp);
          DEC_NODE_COUNT_SINGLY_LL(ll);
          return 0;
      }
  
      /* if node is the only node in LL*/
      if (ll->node_count == 1 && GET_HEAD_SINGLY_LL(ll) == node)
      {
          free(node);
          GET_HEAD_SINGLY_LL(ll) = NULL;
          DEC_NODE_COUNT_SINGLY_LL(ll);
          return 0;
      }
  
      /*if node is the last node of the LL*/
      trav = GET_HEAD_SINGLY_LL(ll);
      while (trav->next != node)
      {
          trav = trav->next;
          continue;
      }
      
      trav->next = NULL;
      free(node);
      DEC_NODE_COUNT_SINGLY_LL(ll);
      return 0;
  }
  
  int singly_ll_remove_node(ll_t *ll, singly_ll_node_t *node)
  {
  	if (!ll || !GET_HEAD_SINGLY_LL(ll)) return 0;
  	if (!node)
      {
  		printf("%s(%d) : Error : node is NULL\n", __FUNCTION__, __LINE__);
  		return -1;		
  	}
  	int i = 0;
  	singly_ll_node_t *head = GET_HEAD_SINGLY_LL(ll), *prev = NULL;
  	
  	if (head == node)
      {
  		GET_HEAD_SINGLY_LL(ll) = GET_NEXT_NODE_SINGLY_LL(head);
  		DEC_NODE_COUNT_SINGLY_LL(ll);
  		node->next = NULL;
  		return 0;
  	}
  
  	prev = head;
  	head = GET_NEXT_NODE_SINGLY_LL(head);
  	for (i =1; i < GET_NODE_COUNT_SINGLY_LL(ll); i++)
      {
  		if (head != node)
          {
  			prev = head;
  			head = GET_NEXT_NODE_SINGLY_LL(head);
  			continue;
  		}
  		
  		GET_NEXT_NODE_SINGLY_LL(prev) = GET_NEXT_NODE_SINGLY_LL(head);
  		GET_NEXT_NODE_SINGLY_LL(head) = NULL;
  		DEC_NODE_COUNT_SINGLY_LL(ll);
  		return 0;	
  	}	
  	printf("%s(%d) : Error : node not found\n", __FUNCTION__, __LINE__);
  	return -1;
  }
  
  unsigned int singly_ll_delete_node_by_value(ll_t *ll, void *data, int size)
  {
      if (!ll || !GET_HEAD_SINGLY_LL(ll)) return 0;
      unsigned int curren_node_count = GET_NODE_COUNT_SINGLY_LL(ll);
      singly_ll_node_t* trav = GET_HEAD_SINGLY_LL(ll);
      while (trav != NULL)
      {
          if (memcmp(trav->data, data, size) == 0)
          {
              singly_ll_delete_node(ll, trav);
  	    	return curren_node_count - GET_NODE_COUNT_SINGLY_LL(ll);
          }
          trav = trav->next;
      }
      return curren_node_count - GET_NODE_COUNT_SINGLY_LL(ll);
  }
  
  unsigned int singly_ll_remove_node_by_value(ll_t *ll, void *data, int size)
  {
      if (!ll || !GET_HEAD_SINGLY_LL(ll)) return 0;
      unsigned int curren_node_count = GET_NODE_COUNT_SINGLY_LL(ll);
      singly_ll_node_t* trav = GET_HEAD_SINGLY_LL(ll);
      while (trav != NULL)
      {
          if (memcmp(trav->data, data, size) == 0)
          {
              singly_ll_remove_node(ll, trav);
  	    	return curren_node_count - GET_NODE_COUNT_SINGLY_LL(ll);
          }
          trav = trav->next;
      }
      return curren_node_count - GET_NODE_COUNT_SINGLY_LL(ll);
  }
  
  unsigned int singly_ll_remove_node_by_dataptr(ll_t *ll, void *data)
  {
      if (!ll || !GET_HEAD_SINGLY_LL(ll)) return 0;
      unsigned int curren_node_count = GET_NODE_COUNT_SINGLY_LL(ll);
      singly_ll_node_t* trav = GET_HEAD_SINGLY_LL(ll);
      while (trav != NULL)
      {
          if (trav->data == data)
          {
              singly_ll_remove_node(ll, trav);
              return curren_node_count - GET_NODE_COUNT_SINGLY_LL(ll);
          }
          trav = trav->next;
      }
      return curren_node_count - GET_NODE_COUNT_SINGLY_LL(ll);
  }
  
  singly_ll_node_t* singly_ll_get_node_by_data_ptr(ll_t *ll, void *data)
  {
  	if (!ll || !GET_HEAD_SINGLY_LL(ll)) return NULL;
  	int i = 0;
  	singly_ll_node_t *head = GET_HEAD_SINGLY_LL(ll);
  
  	for ( ; i < GET_NODE_COUNT_SINGLY_LL(ll); i++)
      {
  		if (head->data == data)
  			return head;
  		head = GET_NEXT_NODE_SINGLY_LL(head);			
  	}
  	return NULL;
  }
  
  void print_singly_LL(ll_t *ll)
  {
      if (!ll)
      {
          printf("Invalid Linked List\n"); 
          return;
      }
      if (is_singly_ll_empty(ll))
      {
          printf("Empty Linked List\n");
          return;
      }
      
      singly_ll_node_t* trav = GET_HEAD_SINGLY_LL(ll);
      unsigned int i = 0;
      printf("node count = %d\n", GET_NODE_COUNT_SINGLY_LL(ll));
      while (trav)
      {
          printf("%d. Data = %p, node = %p\n", i, trav->data, trav);
          i++;
          trav = trav->next;
      }
  }
  
  bool_t is_singly_ll_empty(ll_t *ll)
  {
      if (!ll) assert(0);
      if (ll->node_count == 0)
          return LL_TRUE;
      return LL_FALSE;
  }
  
  void reverse_singly_ll(ll_t *ll)
  {
     if (!ll) assert(0) ;
     if (is_singly_ll_empty(ll)) return;
     if (GET_NODE_COUNT_SINGLY_LL(ll) == 1) return;
     singly_ll_node_t *p1 = GET_HEAD_SINGLY_LL(ll), *p2 = ll->head->next, *p3 = NULL;
     p1->next = NULL;
     do {
          p3 = p2->next;
          p2->next = p1;
          p1 = p2;
          p2 = p3;
     } while(p3);
     ll->head = p1;
     return;
  }
  
  void delete_singly_ll(ll_t *ll)
  {
  	if (!ll) return;
  
  	if(is_singly_ll_empty(ll))
      {
  		return;
  	}
  
  	singly_ll_node_t *head = GET_HEAD_SINGLY_LL(ll), *next = GET_NEXT_NODE_SINGLY_LL(head);
  
  	do {
  		free(head);
  		head = next;
  		if(next)
  			next = GET_NEXT_NODE_SINGLY_LL(next);
  	} while(head);
  
  	ll->node_count = 0;
  	ll->head = NULL;
  }
  
  void singly_ll_set_comparison_fn(ll_t *ll, int (*comparison_fn)(void *, void *))
  {
      if (!ll) assert(0);
      ll->comparison_fn = comparison_fn;
  }
  
  void singly_ll_set_order_comparison_fn(ll_t *ll, int (*order_comparison_fn)(void *, void *))
  {
      if (!ll) assert(0);
      ll->order_comparison_fn = order_comparison_fn;
  }
  
  void* singly_ll_search_by_key(ll_t *ll, void *key)
  {
      assert(ll);
      if (!key)
          return NULL;
  
      singly_ll_node_t *list_node = NULL;
      ITERATE_LIST_BEGIN(ll, list_node)
      {
          if (ll->comparison_fn(list_node->data, key))
              return list_node->data;
      } ITERATE_LIST_END;
      return NULL;
  }
  
  void singly_ll_delete_node_by_data_ptr(ll_t *ll, void *data)
  {
      if (!data)
          return;
  
      singly_ll_node_t *list_node = singly_ll_get_node_by_data_ptr(ll, data);
  
      if (!list_node) 
          return;
      
      singly_ll_remove_node(ll, list_node);
      free(list_node);
      list_node = NULL;
  }
  
  #if 0
  void singly_ll_delete_data_by_key(ll_t *list, void *key)
  {
      singly_ll_node_t *list_node = NULL;
      singly_ll_node_t *list_node_prev = NULL;
      void *data = NULL;
  
      ITERATE_LIST_BEGIN(list, list_node)
      {
          data = list_node->data;
          if(list->comparison_fn(data, key) == 0)
          {
              list_node_prev = list_node;        
              continue;
          }        
      } ITERATE_LIST_END;
  }
  #endif
  
  void singly_ll_add_ordered_data(ll_t *ll, void *data)
  {
      singly_ll_node_t *list_node_prev = NULL,*list_node_next = NULL;
  
      if (is_singly_ll_empty(ll))
      {
          singly_ll_add_node_by_val(ll, data);
          return;
      }
  
      /* Only one node*/
      if (GET_NODE_COUNT_SINGLY_LL(ll) == 1)
      {
          if(ll->comparison_fn(ll->head->data, data) == -1)
          {
              singly_ll_add_node_by_val(ll, data);
          }
          else
          {
              singly_ll_node_t *new_node = singly_ll_init_node(data);
              ll->head->next = new_node;
              INC_NODE_COUNT_SINGLY_LL(ll);
          }
          return;
      }
      
      if (ll->comparison_fn(data, ll->head->data) == -1)
      {
          singly_ll_node_t *new_node = singly_ll_init_node(data);
          new_node->next = GET_HEAD_SINGLY_LL(ll);
          ll->head = new_node;
          INC_NODE_COUNT_SINGLY_LL(ll);
          return;
      }
      
      ITERATE_LIST_BEGIN(ll, list_node_next)
      {
          if(ll->comparison_fn(data, list_node_next->data) != -1)
          {
              list_node_prev = list_node_next;
              continue;
          }
  
          singly_ll_node_t *new_node = singly_ll_init_node(data);
          new_node->next = list_node_next;
          list_node_prev->next = new_node;
          INC_NODE_COUNT_SINGLY_LL(ll);
          return;
  
      } ITERATE_LIST_END;
  
      /* Add in the end*/
      singly_ll_node_t *new_node = singly_ll_init_node(data);
      list_node_prev->next = new_node;
      INC_NODE_COUNT_SINGLY_LL(ll);
  }
  ```






## References

Sagar, A. (2022). *Part A - Multithreading & Thread Synchronization - Pthreads* [Video file]. Retrieved from  https://www.udemy.com/course/multithreading_parta/

