```c
#include <stdio.h>
#include <pthread.h>

pthread_mutex_t work_mutex; 

static int index = 0;
	
void thread_1(void)
{
	while(index<6)
	{
	    printf("This is a pthread_1.\n");

		pthread_mutex_lock(&work_mutex);
		index++;
  		//pthread_mutex_unlock(&work_mutex);
         
        sleep(1);
	}
	
	pthread_exit(0);

}

void thread_2(void)
{
	while(index<6)
	{
	    printf("This is a pthread_2.\n");

		pthread_mutex_lock(&work_mutex);
		index++;
  		pthread_mutex_unlock(&work_mutex);
         
        sleep(1);
	}
	
	pthread_exit(0);
}

void thread_3(void)
{
	while(1)
	{
		char buf[16];
		memset(buf, 0, sizeof(buf));
		strcpy(buf, "thread_3");
	    //printf("This is a pthread_3.\n");
	}
	
	pthread_exit(0);
}

int main(void)
{
    pthread_t id_1,id_2,id_3;
    int i,ret;
    ret = pthread_mutex_init(&work_mutex, NULL); //init mutex 
    if (ret != 0) 
	{
        perror("Mutex initialization failed");
        return -1;
    }
	

    ret=pthread_create(&id_1,NULL,(void  *) thread_1,NULL);
    if(ret!=0)
    {
        printf("Create pthread error!\n");
		return -1;
    }

    ret=pthread_create(&id_3,NULL,(void  *) thread_3,NULL);
    if(ret!=0)
    {
        printf("Create pthread error!\n");
		return -1;
    }


    ret=pthread_create(&id_2,NULL,(void  *) thread_2,NULL);
    if(ret!=0)
    {
        printf("Create pthread error!\n");
		return -1;
    }	


    pthread_join(id_1,NULL);
    pthread_join(id_2,NULL);
    pthread_join(id_3,NULL);
	pthread_mutex_destroy(&work_mutex);
    return 0;
}
```