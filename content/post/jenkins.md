---
title: "pipeline中Jenkinsfile实例"
summary: "pipeline中Jenkinsfile实例"
date: 2021-04-15T13:00:29+08:00
categories: ["tech"]
---

### Jenkinsfile 文件

```
pipeline {
    agent any
    stages {
        stage('unittest and cover report') {
            steps {
                script {
                    GIT_AUTHOR = sh (script: 'git log -1 --pretty=%cn ${GIT_COMMIT}', returnStdout: true).trim()
                }
                sh 'make mock'
                sh 'make lint'
                sh 'make test'
                sh 'make cover'
                sh 'make coverage'
            }
            post {
                success {
                    cobertura coberturaReportFile: '**/coverage.xml'
                    dingtalk (
                        robot: '',
                        type: 'MARKDOWN',
                        at: [],
                        atAll: false,
                        title: '[pipeline jenkins] 通知提醒',
                        text: [
                            "- 状态: <font color=#008000>成功</font>",
                            "- 作者: ${GIT_AUTHOR}",
                            "- 项目名称：${JOB_NAME}",
                            "- 构建编号：${BUILD_NUMBER}",
                            "- 构建日志：${BUILD_URL}",
                        ],
                    )
                }
                failure {
                  dingtalk (
                        robot: '',
                        type: 'MARKDOWN',
                        at: [],
                        atAll: false,
                        title: '[pipeline jenkins] 通知提醒',
                        text: [
                            "- 状态: <font color=#DC143C>失败</font>",
                            "- 作者: ${GIT_AUTHOR}",
                            "- 项目名称：${JOB_NAME}",
                            "- 构建编号：${BUILD_NUMBER}",
                            "- 构建日志：${BUILD_URL}",
                        ],
                   )
                }
            }
        }
    }
}


```


### 参考

https://www.cnblogs.com/chenyishi/p/10943352.html
