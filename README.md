# Jenkins Autograder - JAG

## Requirements 
1. A Git Organization to host all student, assignment-base and solution repos
2. A Git Classroom page linked to the github Organization
3. A Jenkins instance with a working webhook to the Git Organization (com or enterprise) and a setup GitHub Organization Jenkins pipeline

## Note:
###### Jenkins 
Ensure that when using Jenkins matrix authentication, you disable public access. 

## Summary
Jenkins is a task automation software commonly used by software developers to run continoues integration and deployment on their code. As such, it lends itself perfectly to execute autograding tasks on student repos without the students having access to the solutions. 

###### Currently Supported Autograding
1. Style Grading 
2. Console Tests
3. Unit Test
