// #########################################################################
//                          DO NOT EDIT THIS FILE                        
// #########################################################################

def get_files(String directory = "") {
  dir(directory) {
    try {
      sh "find ./*.py -exec  basename {} .py \\; > listFiles"
      return readFile( "listFiles" ).split( "\\r?\\n" )
    } catch (Exception e) {
      return []
    }
  }
}

 def isDirEmpty() {
    def myDirectory = sh(script: "ls", returnStdout: true).trim()
    println(myDirectory)
    return null == myDirectory || "".equals(myDirectory)
  }

void grading_output() {
  sh "touch ${WORKSPACE}/grading_output.txt"
  sh 'echo "#####################################" >> ${WORKSPACE}/grading_output.txt'
  sh 'echo "Built on: ${BUILD_TIMESTAMP}" >> ${WORKSPACE}/grading_output.txt'
  sh 'echo "#####################################" >> ${WORKSPACE}/grading_output.txt'
}

void push_to_git() {
    withCredentials([usernamePassword(credentialsId: 'github-alex', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
        try {
            sh "git checkout -b autograder"
        } catch (Exception e) {
            sh "echo Branch Already exists"
        }  
        sh "git add grading_output.txt"
        sh 'git commit -m "[JAG] Run at ${BUILD_TIMESTAMP}"'
        sh 'git push -f https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${org}/${student_repo_name}.git HEAD:autograder'
    }
}

void run_style_checker(String file = "") {
  dir("") {
    sh 'echo " " >> ${WORKSPACE}/grading_output.txt'
    sh 'echo "-------------------------------------" >> ${WORKSPACE}/grading_output.txt'
    sh "echo Running style checker on: ${file} >> ${WORKSPACE}/grading_output.txt"
    sh "echo *Note if empty: no style errors found* >> ${WORKSPACE}/grading_output.txt"
    sh 'echo " " >> ${WORKSPACE}/grading_output.txt'
    catchError {
      sh "python3 -m pycodestyle --first ${file}.py >> ${WORKSPACE}/grading_output.txt"
    }
    sh 'echo " " >> ${WORKSPACE}/grading_output.txt'
  }
}

void run_unit_tests() {
  dir("hidden") {
    if(!isDirEmpty()) {
      sh 'echo "-------------------------------------" >> ${WORKSPACE}/grading_output.txt'
      sh "echo Running function tests... >> ${WORKSPACE}/grading_output.txt"
      sh 'echo " " >> ${WORKSPACE}/grading_output.txt'
      catchError {
        sh "cp -R ${WORKSPACE}/*.py ."
        sh "python3 grading_tests.py 2>> ${WORKSPACE}/grading_output.txt"
      }
    }
  }
}

void run_console_tests(String file = "") {
  dir("console") {
    if (!isDirEmpty()) {
      sh 'echo "-------------------------------------" >> ${WORKSPACE}/grading_output.txt'
      sh 'echo "Running input/output tests ..." >> ${WORKSPACE}/grading_output.txt'
      sh 'echo " " >> ${WORKSPACE}/grading_output.txt'
      sh 'echo HERE'
      sh "echo ${file}"
      sh(script: "ls *${file}_in* > filecount", returnStatus: true)
      console_file_count = readFile( "filecount" ).split( "\\r?\\n" )
        for(int i = 1;i<= console_file_count.size();i++) {
          sh 'echo "-----------------" >> ${WORKSPACE}/grading_output.txt'
          sh "echo Test ${i} for ${file}: >> ${WORKSPACE}/grading_output.txt"
          sh 'echo " " >> ${WORKSPACE}/grading_output.txt'
          catchError {
            sh(script: "python3 ${WORKSPACE}/${file}.py < ${file}_in${i}.txt > out.txt", returnStatus: false)
            sh(script: "diff -bwis out.txt ${file}_out${i}.txt >> ${WORKSPACE}/grading_output.txt", returnStatus: false)
          }
        //sh "rm filecount"
        sh 'echo " " >> ${WORKSPACE}/grading_output.txt'
      }
    }
  }
}

pipeline {
  agent any
  environment {
    def student_repo_name = "${env.GIT_URL.replaceFirst(/^.*\/([^\/]+?).git$/, '$1')}"
    def assignment = student_repo_name.split("-").first()
    def student_repo = "${env.WORKSPACE}"
    def org = "khoury-sp20-cs5001-align"
  }

 stages {
     stage('Create necassary files') {
           steps {
             script {
               String[] assign_list = student_repo_name.split("-")
               def solutions_repo = sh(script: "grep -A1 '${org}/${assignment}.*${assign_list[1]}.*solution/master' ${env.workspace_pwd}/workspaces.txt", returnStdout: true).split("\n").last()
              
              sh 'rm -rf hidden'
              sh 'rm -rf console'  
               
              sh 'mkdir -p hidden'
              sh 'mkdir -p console'
              try {
                sh "cp -R ${env.workspace_pwd}/${solutions_repo}/hidden/ ."
              } catch (Exception e) {
                  sh 'echo no hidden dir'
              }
              try {
                sh "cp -R ${env.workspace_pwd}/${solutions_repo}/console ."
              } catch (Exception e) {
                  sh 'echo no console dir'
              }

             }
           }
        } 
      stage('Get Submitted Files ') {
        steps {
              script {
              grading_output()
                assignment_files = get_files("${WORKSPACE}")
                run_unit_tests()
                assignment_files.each { file ->
                  run_style_checker(file)
                  run_console_tests(file)
                }
                sh "echo END >> ${WORKSPACE}/grading_output.txt"
                push_to_git()
                }
            }
        } 
  } //STAGES
} // PIPELINE END
