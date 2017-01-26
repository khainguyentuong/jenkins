#!/usr/bin/env groovy
 
/**
 * Jenkinsfile for Jenkins2 Pipeline
 */
 
import hudson.model.*
import hudson.EnvVars
import groovy.json.JsonSlurperClassic
import groovy.json.JsonBuilder
import groovy.json.JsonOutput
import java.net.URL

def notify(message) {
	emailext(
		to: "khainguyentuong@gmail.com",
		subject: "${message}: ${env.JOB_NAME} (${env.BUILD_NUMBER})",
		body: "<a href=${env.BUILD_URL}>${env.JOB_NAME}</a>"
	)
}

try {
	timestamps {
		node ("linux") {
			stage("Pull Code") {
				git branch: "master",
					url: "https://github.com/spring-projects/spring-boot.git"
			}
			
			dir("spring-boot-samples/spring-boot-sample-atmosphere") {
				stage("Build and Test") {
					sh "mvn clean package"
				}    

				stage("Run SonarQube Analysis") {
					def scannerHome = tool "SonarQubeScanner";
					sh "${scannerHome}/bin/sonar-scanner -Dsonar.host.url=http://10.154.130.220/sonar -Dsonar.verbose=true -Dsonar.projectKey=${env.JOB_NAME} -Dsonar.projectVersion=1.0.${env.BUILD_NUMBER} -Dsonar.sources=."
				}				

				stage("Archive Artifacts") {
					archiveArtifacts artifacts: 
						"target/*.jar",
						onlyIfSuccessful: true
				}
			}
		
			stage("Execute Funtional Test") {
				parallel test1: {
					println "Test1..."
					sleep(5)
				}, 
				test2: {
					println "Test2..."
					sleep(5)
				}, 
				test3: {
					println "Test3..."
					sleep(5)
				},
				test4: {
					println "Test4..."
					sleep(5)
				},
				test5: {
					println "Test5..."
					sleep(5)
				}				
			}	
		}
		
		input "Deploy to Dev?"
		
		stage name: "Deploy to Dev", concurrency: 1
		node ("linux") {
			println "Deploy..."
			sleep(10)	
		}
		
		input "Deploy to QA?"
		
		stage name: "Deploy to QA", concurrency: 1
		node ("linux") {
			println "Deploy..."
			sleep(10)	
		}

		input "Deploy to Production?"
		
		stage name: "Deploy to Production", concurrency: 1
		node ("linux") {
			println "Deploy..."
			sleep(10)	
		}		
	}
}	
catch (err) {
	notify("Error ${err}")
	currentBuild.result = "FAILURE"
}