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
		node {
			stage("Pull Code") {
				git "https://github.com/spring-projects/spring-boot.git"
			}
			
			dir("spring-boot-samples/spring-boot-sample-atmosphere") {
				stage("Build and Test") {
					sh "mvn clean package"
				}    

				stage("Run SonarQube Analysis") {
					def scannerHome = tool "SonarQubeScanner";
					withSonarQubeEnv('My SonarQube Server') {
						sh "${scannerHome}/bin/sonar-scanner"
					}
				}				
				
				stage("Archive Artifacts") {
					archiveArtifacts artifacts: 
						"target/*.jar",
						onlyIfSuccessful: true
				}
			}
		}
	}
}	
catch (err) {
	notify("Error ${err}")
	currentBuild.result = "FAILURE"
}