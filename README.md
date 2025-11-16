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
Q.1) Write a C Program that demonstrates redirection of standard output to a file.
#include <stdio.h>
int main() {
 // File pointer to store the redirected output
 FILE *file_ptr;
 // Open the file in write mode to redirect output
 file_ptr = freopen("output.txt", "w", stdout);
 if (file_ptr == NULL) {
 perror("Error opening file");
 return 1;
 }
 // Printing to stdout (redirected to file)
 printf("This output is redirected to a file.\n");
 // Closing the file pointer
 fclose(file_ptr);
 return 0;
Downloaded by Aritc369 (artic1899@gmail.com) }
Output : 
This output is redirected to a file.
Q.2) Implement the following unix/linux command (use fork, pipe and exec system call) 
ls –l | wc –l
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/wait.h>
int main() {
 int pipe_fd[2];
 if (pipe(pipe_fd) == -1) {
 perror("Pipe creation failed");
 exit(EXIT_FAILURE);
 }
 pid_t pid = fork();
 if (pid < 0) {
 perror("Fork failed");
 exit(EXIT_FAILURE);
 } else if (pid == 0) { // Child process
 close(pipe_fd[0]); // Close reading end of pipe in child
 dup2(pipe_fd[1], STDOUT_FILENO); // Redirect stdout to pipe write end// Execute ls -l command
 execlp("ls", "ls", "-l", NULL);
 // execlp will not return unless there's an error
 perror("exec ls -l failed");
 exit(EXIT_FAILURE);
 } else { // Parent process
 close(pipe_fd[1]); // Close writing end of pipe in parent
 dup2(pipe_fd[0], STDIN_FILENO); // Redirect stdin to pipe read end
 // Execute wc -l command
 execlp("wc", "wc", "-l", NULL);
 // execlp will not return unless there's an error
 perror("exec wc -l failed");
 exit(EXIT_FAILURE);
 }
 return 0;
}
Output :
7
