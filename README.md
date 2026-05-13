# Linux-IPC-Shared-memory
Ex06-Linux IPC-Shared-memory

# AIM:
To Write a C program that illustrates two processes communicating using shared memory.

# DESIGN STEPS:

### Step 1:

Navigate to any Linux environment installed on the system or installed inside a virtual environment like virtual box/vmware or online linux JSLinux (https://bellard.org/jslinux/vm.html?url=alpine-x86.cfg&mem=192) or docker.

### Step 2:

Write the C Program using Linux Process API - Shared Memory

### Step 3:

Execute the C Program for the desired output. 

# PROGRAM:
```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/sem.h>
#include <sys/wait.h>
#include <time.h>

#define NUM_LOOPS 10

union semun {
    int val;
    struct semid_ds *buf;
    unsigned short *array;
};

void wait_semaphore(int sem_set_id) {
    struct sembuf sem_op;

    sem_op.sem_num = 0;
    sem_op.sem_op = -1;
    sem_op.sem_flg = 0;

    semop(sem_set_id, &sem_op, 1);
}

void signal_semaphore(int sem_set_id) {
    struct sembuf sem_op;

    sem_op.sem_num = 0;
    sem_op.sem_op = 1;
    sem_op.sem_flg = 0;

    semop(sem_set_id, &sem_op, 1);
}

int main() {

    int sem_set_id;
    union semun sem_val;
    int child_pid;

    sem_set_id = semget(IPC_PRIVATE, 1, 0600);

    if (sem_set_id == -1) {
        perror("semget");
        exit(1);
    }

    printf("Semaphore set created, semaphore set id %d\n", sem_set_id);

    sem_val.val = 0;

    if (semctl(sem_set_id, 0, SETVAL, sem_val) == -1) {
        perror("semctl");
        exit(1);
    }

    child_pid = fork();

    if (child_pid < 0) {
        perror("fork");
        exit(1);
    }

    if (child_pid == 0) {

        for (int i = 0; i < NUM_LOOPS; i++) {

            wait_semaphore(sem_set_id);

            printf("Consumer : %d\n", i);

            fflush(stdout);
        }

        exit(0);

    } else {

        for (int i = 0; i < NUM_LOOPS; i++) {

            printf("Producer : %d\n", i);

            fflush(stdout);

            signal_semaphore(sem_set_id);

            usleep(500000);
        }

        wait(NULL);

        semctl(sem_set_id, 0, IPC_RMID, sem_val);

        printf("Semaphore Removed\n");
    }

    return 0;
}

```

## Write a C program that illustrates two processes communicating using shared memory.

## OUTPUT
<img width="633" height="288" alt="image" src="https://github.com/user-attachments/assets/7855f23e-c94f-41f4-937a-b0fd0a155025" />

<img width="640" height="180" alt="image" src="https://github.com/user-attachments/assets/6c47d82e-fc6b-43da-b384-45438bde77e1" />



# RESULT:
The program is executed successfully.
