properties([parameters(
    [string(defaultValue: 'test', description: 'test or verify', name: 'testType', trim: true),
    string(defaultValue: 'openjdk8', description: 'openjdk8, openjdk9, openjdk10', name: 'jdkVer', trim: true),
    string(defaultValue: 'main', description: 'master, or failed', name: 'branch', trim: true)
    ]
    
    )])

node {
    stage("git") {
        git "https://github.com/hyunil-shin/java-maven-junit-helloworld.git"
    }
    
    manager.addShortText(params.jdkVer)
    manager.addShortText(params.testType)
    
    stage('build') {
        // ,
        //        "PATH+JDK=${tool 'openjdk10'}/bin"
        // mvn: mvn-3.3.9, mvn-3.6.0, mvn-3.6.2
        // jdk: openjdk8, openjdk9, openjdk10
        def jdk = tool params.jdkVer
        withEnv(["PATH+MAVEN=${tool 'mvn-3.3.9'}/bin", 
                "JAVA_HOME=${jdk}"]) {
            sh 'ls -al $JAVA_HOME'
            sh 'mvn --version'
            sh "mvn clean ${params.testType} -Dmaven.test.failure.ignore=true"
        }
    }
    
    stage('report') {
        junit 'target/surefire-reports/*.xml'
        jacoco execPattern: 'target/**.exec'
        
        // if failed tests exist
        if (currentBuild.result == 'UNSTABLE') {
            
            manager.addShortText("you have got fails.")
            mail bcc: '', body: "<a href='${BUILD_URL}'>jenkins</a>", cc: '', from: '', replyTo: '', subject: 'you have failed tests', to: 'hyejo.kwon@nhnent.dooray.com', mimeType: "text/html"
            
            // dooray messenger
            def messengerBody = "you have failed tests"
             {
 
                httpRequest httpMode: 'POST', 
                    requestBody: """{"botName": "Jenkins Pipeline Training", "botIconImage": "https://static.dooray.com/static_images/dooray-bot.png", "text":"${messengerBody}",
                        "attachments" : [
                            {
                                "title" : "jenkins",
                                "titleLink" : "${BUILD_URL}",
                                "text" : "",
                                "color" : "red"
                            }
                        ]
                    }""", 
                    responseHandle: 'NONE', url: "https://hook.dooray.com/services/3038343050984607626/3580466600959904703/crmW_SVjRrOueppzT0Dbxw", contentType: 'APPLICATION_JSON'
            }
                    
        }else {
            manager.addShortText("good job")
            
            def messengerBody = "good job"
            {
                httpRequest httpMode: 'POST', 
                    requestBody: """{"botName": "Jenkins Pipeline Training", "botIconImage": "https://static.dooray.com/static_images/dooray-bot.png", "text":"Hi This Is Test",
                        "attachments" : [
                            {
                                "title" : "jenkins",
                                "titleLink" : "${BUILD_URL}",
                                "text" : "",
                                "color" : "blue"
                            }
                        ]
                    }""", 
                    responseHandle: 'NONE', url: "https://hook.dooray.com/services/3038343050984607626/3580466600959904703/crmW_SVjRrOueppzT0Dbxw", contentType: 'APPLICATION_JSON'
            }
        }
    }
    
    
}
