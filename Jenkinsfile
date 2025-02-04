/*
	Copyright CESSDA ERIC 2017-2019

	Licensed under the Apache License, Version 2.0 (the "License"); you may not
	use this file except in compliance with the License.
	You may obtain a copy of the License at
	https://www.apache.org/licenses/LICENSE-2.0

	Unless required by applicable law or agreed to in writing, software
	distributed under the License is distributed on an "AS IS" BASIS,
	WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
	See the License for the specific language governing permissions and
	limitations under the License.
*/
pipeline {

	options {
		buildDiscarder logRotator(numToKeepStr: '20', artifactNumToKeepStr: '5')
	}

	environment {
		productName = 'guidelines'
		componentName = 'public'
		imageTag = "${docker_repo}/${productName}-${componentName}:${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
		scannerHome = tool 'sonar-scanner'
	}

	agent any

	stages {
		// Compiles documentation
		stage('Build Documentation') {
			agent {
				dockerfile {
					filename 'jekyll.Dockerfile'
					reuseNode true
				}
			}
			stages {
				stage('Lint Documentation') {
					steps {
						sh "bundle exec rake lint"
					}
				}
				stage('Build Deployable Documentation') {
					steps {
						sh "jekyll build"
						sh "bundle exec rake htmlproofer"
					}
					/*when { branch 'master' }*/
				}
				// Corrects links so that the Jenkins preview works
				stage('Build Test Documentation') {
					steps {
						sh "sed -i s#URL#\"https://jenkins.cessda.eu\"#g _config.jenkins.yml"
						sh "sed -i s#BASE#\"/job/cessda.guidelines.public/job/${env.BRANCH_NAME}/${env.BUILD_NUMBER}/Build_20Result/\"#g _config.jenkins.yml"
						sh "sed -i s#JENKINSJOB#\"https://jenkins.cessda.eu/job/cessda.guidelines.public/job/${env.BRANCH_NAME}/${env.BUILD_NUMBER}/\"#g _config.jenkins.yml"
						sh "jekyll build --config _config.yml,_config.jenkins.yml"
					}
					when { not { branch 'main' } }
					post {
						success {
							publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: true, reportDir: '_site/', reportFiles: 'index.html', reportName: 'Build Result', reportTitles: ''])
						}
					}
				}
			}
		}
		stage('Run SonarQube Scan') {
			steps{
				withSonarQubeEnv('cessda-sonar') {
					nodejs('node-14') {
						sh "${scannerHome}/bin/sonar-scanner"
					}
				}
				timeout(time: 1, unit: 'HOURS') {
					waitForQualityGate abortPipeline: true
				}
			}
			when { branch 'main' }
		}
		stage('Build Nginx Container') {
			steps {
				sh "docker build -t ${imageTag} -f nginx.Dockerfile ."
			}
			when { branch 'main' }
		}
		stage('Push Docker Container') {
			steps {
				sh "gcloud auth configure-docker"
				sh "docker push ${imageTag}"
				sh "gcloud container images add-tag ${imageTag} ${docker_repo}/${productName}-${componentName}:${env.BRANCH_NAME}-latest"
			}
			when { branch 'main' }
		}
		stage('Deploy Guidelines') {
			steps {
				build job: 'cessda.guidelines.deploy/master', parameters: [string(name: 'imageTag', value: "${env.BRANCH_NAME}-${env.BUILD_NUMBER}")]
			}
			when { branch 'main' }
		}
	}
}
