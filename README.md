# Jenkins Autograder - JAG

## Requirements 
1. A Git Organization to host all student, assignment-base and solution repos
2. A Git Classroom page linked to the github Organization
3. A Jenkins instance with a working webhook to the Git Organization (com or enterprise) and a setup GitHub Organization Jenkins pipeline

## Note
###### Jenkins 
Ensure that when using Jenkins matrix authentication, you disable public access. 

## Summary
Jenkins is a task automation software commonly used by software developers to run continoues integration and deployment on their code. As such, it lends itself perfectly to execute autograding tasks on student repos without the students having access to the solutions.

###### Currently Supported Languages
1. **Python** *but can be easily adapted with language specific unix commands*

###### Currently Supported Autograding
* Style Grading
  * `python3 -m pycodestyle --first ${file}.py`
* Console Tests
  * `python3 ${WORKSPACE}/${file}.py < ${file}_in${i}.txt > out.txt && diff -bwi out.txt ${file}_out${i}.txt >> ${WORKSPACE}/grading_output.txt`
* Unit Test
  * `python3 ${h_file}.py`

## Jenkinsfile
Two Jekinsfiles are needed. A dummy one for the solutions repo and the one for the base-repo. Without adding a Jenkinsfile to the solutions repo, jenkins will not be able to discover the repo and will not copy over the files to the server, making them inaccessible for the automated grading. 

1. **In the base-repo** copy and paste the Jenkinsfile from this repo
2. **In the solution-repo** create a Jenkinsfile with the following contents:
 
```java
pipeline {
    agent any
    stages {
        stage('Nothing') {
            steps {
                echo 'Nothing to do, need a dummy jenkinsfile so Jenkins can find the repo :)'
            }
        }
    }
}
```

**Envirnoment Variables that need to be set**

**Line 54** --> Fill in your Git Organization

## Solutions Repo Structure 

```bash
├── hidden
│   ├── assignment1_hidden.py
│   └── assignment2_hidden.py
├── console
│   ├── assignment1_in1.txt
│   ├── assignment1_in{x}.txt
│   ├── assignment1_out1.txt
│   ├── assignment1_out{x}.txt
│   ├── assignment2_in2.txt
│   └── assignment2_out2.txt
├── assignment1.py
├── assignment2.py
└── README.md
```

