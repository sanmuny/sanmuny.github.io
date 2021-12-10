---
title: 'Linux系统中的信号量机制'
date: 2014-04-24 14:39:27
tags: []
published: true
hideInList: false
feature: 
isTop: false
---

1、信号量的定义：

    struct semaphore {
        spinlock_t lock;
        unsigned int count;
        struct list_head wait_list;
    };
    

在linux中，信号量用上述结构体表示，我们可以通过该结构体定义一个信号量。

2、信号量的初始化：

可用void sema_init(struct semaphore *sem, int val);直接创建，其中val为信号量初值。也可以用两个宏来定义和初始化信号量的值为1或0：

    DECLARE_MUTEX(name)	:	定义信号量name并初始化为1
    DECLARE_MUTEX_LOCKED(name)	:	定义信号量name并初始化为0
    

还可以用下面的函数初始化：

    void init_MUTEX(struct semaphore *sem);	 //初始化信号量的值为1
    void init_MUTEX_LOCKED(struct semaphore *sem);	//初始化信号量的值为0
    

3、信号量的原子操作：

p操作：

*   void down(struct semaphore *sem); //用来获取信号量，如果信号量值大于或等于0，获取信号量，否则进入睡眠状态，睡眠状态不可唤醒
*   void down_interruptible(struct semephore *sem); //用来获取信号量，如果信号量大于或等于0，获取信号量，否则进入睡眠状态，等待信号量被释放后，激活该程。
*   void down_trylock(struct semaphore *sem); //试图获取信号量，如果信号量已被其他进程获取，则立刻返回非零值，调用者不会睡眠

v操作：

*   void up(struct semaphore *sem); //释放信号量，并唤醒等待该资源进程队列的第一个进程

4、经典同步问题的解决方案：

*   生产者和消费者问题：

a、单缓冲区问题描述：生产者向消费者提供产品，它们共享一个有界缓冲区，生产者向其中投放产品，消费者从中取得产品。同时，每个进程都互斥的占用CPU。假定生产者和消费者是互相等效的，只要缓冲区未满，生产者就可以把产品送入缓冲区，类似的，只要缓冲区未空，消费者便可以从缓冲区中取走产品并消费它。生产者—消费者的同步关系将禁止生产者向已满的缓冲区中放入产品，也禁止消费者从空的缓冲区中获取产品

问题分析： 需要定义两个信号量，一个用于互斥访问缓冲区，另一个用于生产者与消费者之间的同步。s1=1; s2=0;

伪代码：

    生产者进程            
    
    while(1)                                         
    {                                       
        printf(“I'm producing!\n”);                                        
        down_interruptible(&s1);                                         
        printf(“I'm putting a product into the buffer\n”);          
        up(&s1);                   
        up(&s2);                
    }               
    

消费者进程

    while(1)  
    {
        down_interruptible(&s2);
        down_interruptible(&s1);
        printf(“I'm getting a product\n”);
        up(&s1);
        printf(“I'm consuming a product\n”);
    }
    

b、多生产者、多消费者、n个缓冲区

问题描述：有一群生产者进程在生产产品，并将这些产品提供给消费者进程去消费。为使生产者进程与消费者进程并发执行，在两者之间设置了n个缓冲区，生产者将产品放入一个缓冲区中，消费者可以从一个缓冲区中取走产品去消费。要求生产者进程与消费者进程必须保持同步，即不允许生产者进程向一个满的缓冲区放产品，也不允许消费者从一个空的缓冲区取产品。

问题分析：该问题貌似比a问题复杂的多，首先我们定义一个数组buffer\[n\]，来表示n个缓冲区，还需要定义两个变量：in 表示要存入的缓冲区的下标，out表示要取产品的缓冲区的下标。定义三个信号量：s1用于实现对缓冲池的互斥操作，empty表示空缓冲区的个数，full表示满缓冲区的个数。初值：in=out=0; s1=1; full=0; empty=n;

伪代码如下：

    生产者进程                                                                  
    
    while(1)                                                                          
    {                                                                                      
        printf(“I'm producing!\n”);                                                   
        down_interruptible(&empty);                                           
        down_interruptible(&s1);                                                   
        printf(“I'm putting a product into the buffer[in]\n”);                 
                                                                                                              
        in = (in+1)%n;                                                                                
        up(&s1);                                                                                      
        up(&full);                                                                     
    }                                                                                       
    

消费者进程

*   哲学家进餐问题
    
    while(1) { down\_interruptible(&full); down\_interruptible(&s1); printf(“I'm getting a product from buffer\[out\]\\n”); out = (out+1)%n; up(&empty); printf(“I'm consuming a product\\n”); }
    

问题描述：五个哲学家共用一个圆桌，分别坐在周围的五张椅子上，在圆桌上有五只碗和五只筷子，他们交替进行思考和进餐。哲学家饥饿时便试图取最靠近他的两只筷子，当同时获得两只筷子时便可用餐，用餐完毕后放下筷子。

问题分析： 五只筷子为临界资源，定义包含五个元素的信号量数组来实现对筷子的互斥使用。chopstick\[5\]，五个信号量的初值都为1。

伪代码：

第i（i=0,1,2,3,4）个哲学家进程：

    while(1)
    {
              down_interruptible(&chopstick[i]);
              down_interruptible(&chopstick[(i+1)%5]);
              printf(“I'm eating!\n”);
              up(&chopstick[i]);
              up(&chopstick[(i+1)%5);
    }
    

*   读者写者问题：

问题描述：一个文件可被多个进程共享，reader进程读取该文件，而writer进程负责写文件，允许多个reader进程同时读取文件，但不允许一个writer进程和其他reader进程或writer进程同时访问文件。

问题分析：进程对文件互斥访问的实现可借助一个信号量就可以搞定，但是我们需要引入一个count变量来记录reader进程的个数，对这个变量的访问也是互斥的，所以也需要引入一个信号量。定义信号量rs实现对count的互斥访问，定义ws实现对文件的互斥访问。两信号量初值都为1

伪代码：

    reader进程                             	
    {                                                                                          
            down_interruptible(&rs);                                                                                                                                                                                                   
            count++;
            up(&rs);  
            printf(“I'm reading!\n”);   
           down_interruptible(&rs);
           count--;
           if(count == 0){
                   up(&ws);
            }
            up(&rs);
    }       
    
     writer进程
     {
        down_interruptible(&ws);           
        if (count == 0){ 
            printf(“I'm writing!\n”);
            down_interruptible(&ws);                                     
        } 
        up(&ws);
    }