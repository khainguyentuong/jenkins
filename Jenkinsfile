#!/usr/bin/env groovy
 
/**
 * Pipeline
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
			stage ("Pull Code") {
				git (
					branch: "master",
					url: "https://github.com/spring-projects/spring-boot.git"
				)	
			}
			
			dir ("spring-boot-samples/spring-boot-sample-atmosphere") {
				stage ("Build") {
					sh ("mvn clean package")
				}    

				stage ("Run SonarQube Analysis") {
					def scannerHome = tool "SonarQubeScanner";
					sh ("${scannerHome}/bin/sonar-scanner"
						+ " -Dsonar.host.url=http://10.154.130.220/sonar"
						+ " -Dsonar.verbose=true"
						+ " -Dsonar.projectKey=${env.JOB_NAME}"
						+ " -Dsonar.projectVersion=1.0.${env.BUILD_NUMBER}"
						+ " -Dsonar.sources=."
					)
				}				

				stage ("Archive Artifacts") {
					archiveArtifacts (
						artifacts: "target/*.jar",
						onlyIfSuccessful: true
					)	

					stash (
						name: "binary",
						includes: "target/*"
					)
				}
			}
		
			stage ("Deploy to Dev") {
				sh ("sleep 5s")
			}
			
			stage ("Integration Test") {
				parallel (
					test1: {
						sh ("sleep 5s")
					},
					test2: {
						sh ("sleep 5s")
					},
					test3: {
						sh ("sleep 5s")
					}
				)	
			}	
		}
		
		stage ("Promotion...") {
			input ("Deploy to QA?")
		}

		stage ("Deploy to QA") {
			node ("linux") {
				unstash ("binary")
				sh ("sleep 5s")
			}
		}
		
		stage ("Regression Test") {
			node ("linux") {
				sh ("sleep 5s")
			}
		}		
		
		stage ("Promotion...") {
			input ("Deploy to Production?")
		}

		stage ("Deploy to Production") {
			node ("linux") {
				unstash ("binary")
				sh ("sleep 5s")
			}
		}		
		
		stage ("Smoke Test") {
			node ("linux") {
				sh ("sleep 5s")
			}
		}
	}
}	
catch (err) {
	notify("Error ${err}")
	currentBuild.result = "FAILURE"
}