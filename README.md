# mini-shell
mini linux shell c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/wait.h>

#define MAX_LINE 1024
#define MAX_ARGS 64

void printPrompt() {
    char cwd[256];
    getcwd(cwd, sizeof(cwd));
    printf("\nMiniShell:%s$ ", cwd);
}

void execute(char *args[]) {

    pid_t pid = fork();

    if(pid == 0){

        if(execvp(args[0], args) == -1){
            printf("Command not found.\n");
        }

        exit(EXIT_FAILURE);
    }

    else if(pid > 0){
        wait(NULL);
    }

    else{
        printf("Fork failed.\n");
    }

}

int main() {

    char line[MAX_LINE];
    char *args[MAX_ARGS];

    while(1){

        printPrompt();

        if(fgets(line,sizeof(line),stdin)==NULL)
            continue;

        line[strcspn(line,"\n")] = '\0';

        if(strlen(line)==0)
            continue;

        int i=0;

        args[i]=strtok(line," ");

        while(args[i]!=NULL){

            i++;

            args[i]=strtok(NULL," ");

        }

        if(strcmp(args[0],"exit")==0){

            printf("Goodbye!\n");
            break;

        }

        if(strcmp(args[0],"cd")==0){

            if(args[1]==NULL){

                printf("Usage: cd <directory>\n");

            }

            else{

                if(chdir(args[1])!=0){

                    perror("cd");

                }

            }

            continue;

        }

        if(strcmp(args[0],"clear")==0){

            system("clear");
            continue;

        }

        if(strcmp(args[0],"help")==0){

            printf("\nSupported Commands\n");
            printf("------------------\n");
            printf("cd <folder>\n");
            printf("clear\n");
            printf("help\n");
            printf("exit\n");
            printf("All Linux commands (ls, pwd, cat, mkdir...)\n");

            continue;

        }

        execute(args);

    }

    return 0;

}
