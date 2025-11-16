slip 6
Display all the files from current directory which are created in particular month.

#include <stdio.h>
#include <stdlib.h>
#include <dirent.h>
#include <sys/stat.h>

#include <time.h>

int main() {

 DIR *directory;

 struct dirent *entry;

 struct stat fileStat;

 time_t now = time(NULL);

 struct tm *current_time = localtime(&now);
 
 int current_month = current_time->tm_mon + 1; 
 
 // Month is zero-based in struct tm
 // Open the current directory

 directory = opendir(".");

 if (directory == NULL) {

 perror("Unable to open directory");
 
 return EXIT_FAILURE;
 }

 // Read directory entries and display files created in a specific month

 printf("Files created in current month (%d):\n", current_month);

 while ((entry = readdir(directory)) != NULL) {

 if (stat(entry->d_name, &fileStat) == 0) {
 struct tm *file_time = localtime(&fileStat.st_ctime);

 int file_month = file_time->tm_mon + 1; // Month is zero-based in struct tm
 printf("%s\n", entry->d_name);
 }
 }
 }
 closedir(directory);
 return EXIT_SUCCESS;

}
Output :

Files created in current month (12):
file1.txt

Q.2)Write a C program to create n child processes. When all n child processes terminates, 
Display total cumulative time children spent in user and kernel mode.

#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <sys/resource.h>
#include <unistd.h>
int main(int argc, char *argv[]) {
 if (argc != 2) {

 printf("Usage: %s <number_of_children>\n", argv[0]);
 return 1;

 }

 int num_children = atoi(argv[1]);

 struct rusage usage;

 int status;

 pid_t pid;
 for (int i = 0; i < num_children; i++) {

 pid = fork();

 if (pid < 0) {

 perror("Fork failed");

 return 1;

 } else if (pid == 0) { // Child process
 // Perform child's task
 sleep(1);
 exit(EXIT_SUCCESS);
 }
 }

 // Wait for all child processes to terminate
 while ((pid = wait(&status)) > 0);
 // Get resource usage statistics for all children

 if (getrusage(RUSAGE_CHILDREN, &usage) == -1) {

 perror("getrusage failed");
 return 1;
 }

 // Display total cumulative time spent by children in user and kernel mode
 printf("Total user time spent by children: 
 
 %ld.%06ld seconds\n", usage.ru_utime.tv_sec, 
usage.ru_utime.tv_usec);

 printf("Total kernel time spent by children: 
 %ld.%06ld seconds\n", usage.ru_stime.tv_sec, 
usage.ru_stime.tv_usec);

return 0;
}

Output :

Total user time spent by children: 0.000002 seconds

Total kernel time spent by children: 0.000000 seconds
