[Home](../../) | [Notes](../../notes) | [Projects](../) > TCP/IP Group Chat Application

# TCP/IP Group Chat Application



## Introduction

* Developed multi‐threaded client/server socket applications for Linux OS using C and the POSIX thread libraries
* Used Vim for software development, and GDB for debugging



## Implementation & Demonstration

### Implementation

<iframe width="560" height="315" src="https://www.youtube.com/embed/E9aWLi5vbpU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### Demonstration

<iframe width="560" height="315" src="https://www.youtube.com/embed/JLX93Sm8QMQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>



## Development Environment

* **Operating System**

  Ubuntu 22.04.1 LTS (Kernel version: 5.15.0-52-generic)

* **Compiler**

  GCC version 11.3.0 (Ubuntu 11.3.0-1ubuntu1~22.04)

* **Debugger**
  GDB version 12.0.90 (Ubuntu 12.0.90-0ubuntu1)



## Source Code

### Server

* **groupchat_linux_server.c**

  ```c
  //========================================================================================
  // Filename     : groupchat_linux_server.c
  // Description  : Group chat application - Server
  // Author       : Kyungjae Lee
  // History      : 10/20/2022 - Created file
  //                10/26/2022 - Added descriptions to the compilation command flags
  //                10/27/2022 - Updated code comments
  //                11/11/2022 - Modified function name 'print_err' -> 'handle_err'
  //                11/18/2022 - Added screen clear feature 'system("clear")'
  //----------------------------------------------------------------------------------------
  // To compile this program:
  //      gcc groupchat_linux_server.c -D_REENTRANT -o server -lpthread
  //                                   ------------           ---------
  //                                        |                     |
  //                 drives the compiler to use            adds support for multithreading
  //                 thread safe (i.e., re-entrant)        with the pthreads library
  //                 versions of several functions     
  //                 in the C library  
  //
  // To run this program:
  //      ./server <port>
  //
  // [!] Note: For this program to work properly with clients in the different network, TCP
  //           port must be configured using port-forwarding.
  //========================================================================================
  
  #include <stdio.h>
  #include <stdlib.h>
  #include <unistd.h>
  #include <string.h>
  #include <arpa/inet.h>
  #include <sys/socket.h>
  #include <netinet/in.h>
  #include <pthread.h>
  
  #define MSG_LEN     2048
  #define MAX_CLIENT  512
  
  // function declarations
  void* serve_cl(void *arg);
  void broadcast_msg(char *msg, int len);
  void handle_err(char *err_msg);
  
  // global variables 
  int cl_cnt = 0;             // counter for connected clients
  int cl_socks[MAX_CLIENT];   // array (list) of the connected clients
  pthread_mutex_t mutex;  
  
  
  int main(int argc, char *argv[])
  {
      int sv_sock, cl_sock;                   // server/client socket file descriptors
      struct sockaddr_in sv_addr, cl_addr;    // Internet socket address structures
      int cl_addr_sz;                         // size of the client sockaddr_in structure
      pthread_t t_id;                         // thread ID
  
      // handle invalid number of arguments
      if (argc != 2)
      {
          printf("Usage: %s <port>\n", argv[0]);
          exit(1);
      }
  
      pthread_mutex_init(&mutex, NULL);
  
      // create a TCP socket
      sv_sock = socket(PF_INET, SOCK_STREAM, 0);  
  
      // allocate and initialize Internet socket address structure (server info)
      // specify server's IP address and port
      memset(&sv_addr, 0, sizeof(sv_addr));
      sv_addr.sin_family = AF_INET;
      sv_addr.sin_addr.s_addr = htonl(INADDR_ANY);
      sv_addr.sin_port = htons(atoi(argv[1]));
  
      // bind server's address information to the server socket
      if (bind(sv_sock, (struct sockaddr*)&sv_addr, sizeof(sv_addr)) == -1)
          handle_err("ERROR: bind() fail");
  
      // convert the server socket to listening socket
      if (listen(sv_sock, 5) == -1)
          handle_err("ERROR: listen() fail");
  
      // clear the screen
      system("clear");
  
      // print the welcome message to the screen
      printf("Group chat linux server running ... (port: %s)\n", argv[1]);
  
      while (1)
      {
          // get the size of Internet socket address structure
          cl_addr_sz = sizeof(cl_addr);
  
          // accept client connection
          cl_sock = accept(sv_sock, (struct sockaddr*)&cl_addr, &cl_addr_sz);
  
          // add the new client to the list
          pthread_mutex_lock(&mutex);     // enter the critical section   
          cl_socks[cl_cnt++] = cl_sock;  
          pthread_mutex_unlock(&mutex);   // leave the critical section
  
          // create a thread to serve the client
          pthread_create(&t_id, NULL, serve_cl, (void*)&cl_sock); 
  
          // detach the thread from the main thread so that it automatically destroys itself
          // upton termination
          pthread_detach(t_id); 
  
          // print the connected client's IP address to the screen
          printf("Connected client IP: %s\n", inet_ntoa(cl_addr.sin_addr));
      }
  
      close(sv_sock);
     
      return 0;
  } // end of main
  
  // receive client's message, and broadcast it to all connected clients
  void* serve_cl(void *arg)
  {
      int cl_sock = *((int*)arg);
      int str_len = 0, i;
      char msg[MSG_LEN];
  
      // this while loop won't break until the client terminates the connection
      while ((str_len = read(cl_sock, msg, sizeof(msg))) != 0)
          broadcast_msg(msg, str_len);
      
      pthread_mutex_lock(&mutex);    // enter the critical section 
  
      // before terminating the thread, remove itself (disconnected client) from the list
      for (i = 0; i < cl_cnt; i++)    
      {
          if (cl_sock == cl_socks[i])
          {
              while (i < cl_cnt - 1)
              {
                  cl_socks[i] = cl_socks[i + 1];
                  i++;
              }
  
              break;
          }
      }
      
      cl_cnt--;                       // decrement the client counter
      pthread_mutex_unlock(&mutex);   // leave the critical section 
  
      // terminate the connection
      close(cl_sock);
  
      return NULL;
  } // end of serve_cl
  
  // broadcast message to all connected clients
  void broadcast_msg(char *msg, int len)
  {
      int i;
  
      pthread_mutex_lock(&mutex);     // enter the critical section 
  
      // send message to every connected clients
      for (i = 0; i < cl_cnt; i++)
          write(cl_socks[i], msg, len);
  
      pthread_mutex_unlock(&mutex);   // leave the critical section 
  } // end of broadcast_msg
  
  // handle error
  void handle_err(char *err_msg)
  {
      fputs(err_msg, stderr);
      fputc('\n', stderr);
      exit(1);
  } // end of handle_err
  ```

### Client

* **groupchat_linux_client.c**

  ```c
  //========================================================================================
  // Filename     : groupchat_linux_client.c
  // Description  : Group chat application - Client
  // Author       : Kyungjae Lee
  // History      : 10/20/2022 - Created file
  //                10/26/2022 - Added join/leave message broadcasting functionality
  //                           - Added descriptions to the compilation command flags
  //                10/27/2022 - Updated code comments
  //                11/11/2022 - Modified function name 'print_err' -> 'handle_err'
  //                11/18/2022 - Added screen clear feature 'system("clear")'
  //----------------------------------------------------------------------------------------
  // To compile this program:
  //      gcc groupchat_linux_client.c -D_REENTRANT -o client -lpthread
  //                                   ------------           ---------
  //                                        |                     |
  //                 drives the compiler to use            adds support for multithreading
  //                 thread safe (i.e., re-entrant)        with the pthreads library
  //                 versions of several functions     
  //                 in the C library            
  //
  // To run this program:
  //      ./client <serverIP> <port> <name>
  //
  // [!] Note: For this program to work properly with the server in the different network,
  //           there must be a designated TCP port configured on the default gateway of the
  //           network where the server belongs to.
  //========================================================================================
  
  #include <stdio.h>
  #include <stdlib.h>
  #include <unistd.h>
  #include <string.h>
  #include <arpa/inet.h>
  #include <sys/socket.h>
  #include <netinet/in.h>
  #include <pthread.h>
  
  #define MSG_LEN     1024
  #define NAME_LEN    32
  
  // function declarations
  void* send_msg(void *arg);
  void* recv_msg(void *arg);
  void handle_err(char *err_msg);
  
  // global variables 
  char name[NAME_LEN] = "noname";
  char msg[MSG_LEN];
  char info_msg[MSG_LEN];
  
  
  int main(int argc, char *argv[])
  {
      int sock;                           // client socket file descriptor
      struct sockaddr_in sv_addr;         // Internet socket address structure
      pthread_t send_thread, recv_thread; // two threads of the client process
      void *thread_return;                // pointer to the thread's return value
  
      // handle invalid number of arguments
      if (argc != 4)
      {
          printf("Usage: %s <serverIP> <port> <name>\n", argv[0]);
          exit(1);
      }
  
      sprintf(name, "%s", argv[3]);  // setup user name
  
      // create a TCP socket
      sock = socket(PF_INET, SOCK_STREAM, 0); 
  
      // allocate and initialize the Internet socket address structure (server info)
      // specify server's IP address and port
      memset(&sv_addr, 0, sizeof(sv_addr));
      sv_addr.sin_family = AF_INET;
      sv_addr.sin_addr.s_addr = inet_addr(argv[1]);
      sv_addr.sin_port = htons(atoi(argv[2]));
  
      // establish connection with server
      if (connect(sock, (struct sockaddr*)&sv_addr, sizeof(sv_addr)) == -1)
          handle_err("ERROR: connect() fail");
  
      // clear the screen
      system("clear");
  
      // print the welcome message to the screen
      printf("Welcome to group chat! (Q/q to quit)\n\n");
  
      // print the join message to the screen
      sprintf(msg, "+++++ %s has joined! +++++\n", name);
      write(sock, msg, strlen(msg));
  
      // create two threads to carry out send and receive operations, respectively
      pthread_create(&send_thread, NULL, send_msg, (void*)&sock);
      pthread_create(&recv_thread, NULL, recv_msg, (void*)&sock);
      pthread_join(send_thread, &thread_return);
      pthread_join(recv_thread, &thread_return);
  
      // terminate connection
      close(sock);
  
      return 0;
  } // end of main
  
  // send message to the server
  void* send_msg(void *arg)
  {
      int sock = *((int*)arg);
      char name_msg[NAME_LEN + MSG_LEN + 2];  // add '2' for brackets around the name
  
      while (1)
      {
          fgets(msg, MSG_LEN, stdin);
  
          // terminate the connection upon user input Q/q
          if (!strcmp(msg, "q\n") || !strcmp(msg, "Q\n"))
          {
              // print the leave message to the screen
              sprintf(msg, "----- %s has left! -----\n", name);
              write(sock, msg, strlen(msg));
  
              // terminate the connection
              close(sock);
              exit(0);
          }
  
          // construct the message and send it to the server
          sprintf(name_msg, "[%s] %s", name, msg);
          write(sock, name_msg, strlen(name_msg));
      }
      
      return NULL;
  } // end of send_msg
  
  // receive message and print it to the screen
  void* recv_msg(void *arg)
  {
      int sock = *((int*)arg);
      char name_msg[NAME_LEN + MSG_LEN + 2];
      int str_len;
  
      while(1)
      {
          // receive the message broadcasted by the server
          str_len = read(sock, name_msg, NAME_LEN + MSG_LEN + 2 - 1); 
              // add '2' for brackets around the name
  
          // terminate the thread if the client has been disconnected
          if (str_len == -1)
              return (void*)-1;
  
          // print the received message to the screen
          name_msg[str_len] = 0;
          fputs(name_msg, stdout);
      }
  
      return NULL;
  } // end of recv_msg
  
  // handle error
  void handle_err(char *err_msg)
  {
      fputs(err_msg, stderr);
      fputc('\n', stderr);
      exit(1);
  } // end of handle_err
  ```

  