#include <stdio.h>
#include <pthread.h>
#include <stdlib.h>
#include <unistd.h>

// Constants
#define MAX_OPEN_PORTS 5 // Maximum number of ports allowed to be open

// Global variables
int openPorts = 0; // Tracks the number of currently open ports

// Mutex and condition variable
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t portsAvailable = PTHREAD_COND_INITIALIZER;

// Function to simulate requesting a port
void* requestPort(void* thread_id) {
int id = *((int*)thread_id);

pthread_mutex_lock(&mutex); // Lock mutex for thread-safe access
while (openPorts >= MAX_OPEN_PORTS) {
printf("Thread %d: Waiting for a port to become available...\n", id);
pthread_cond_wait(&portsAvailable, &mutex); // Wait for a port to be available
}

// Open a port
openPorts++;
printf("Thread %d: Opened a port. Total open ports: %d\n", id, openPorts);
pthread_mutex_unlock(&mutex); // Unlock mutex

// Simulate work with the port
sleep(2); // Simulating port usage

// Release the port
pthread_mutex_lock(&mutex); // Lock mutex to safely decrement port count
openPorts--;
printf("Thread %d: Released a port. Total open ports: %d\n", id, openPorts);
pthread_cond_signal(&portsAvailable); // Signal waiting threads
pthread_mutex_unlock(&mutex); // Unlock mutex

pthread_exit(NULL);
}

int main() {
const int NUM_THREADS = 10; // Number of threads trying to access ports
pthread_t threads[NUM_THREADS];
int thread_ids[NUM_THREADS];

for (int i = 0; i < NUM_THREADS; i++) {
thread_ids[i] = i + 1;
if (pthread_create(&threads[i], NULL, requestPort, (void*)&thread_ids[i]) != 0) {
perror("Failed to create thread");
exit(1);
}
}

// Join threads
for (int i = 0; i < NUM_THREADS; i++) {
pthread_join(threads[i], NULL);
}

// Clean up resources
pthread_mutex_destroy(&mutex);
pthread_cond_destroy(&portsAvailable);

printf("All threads have completed.\n");
return 0;
}
