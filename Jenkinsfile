/*
 * Hint:
 * 1) Please note the below script will work only if you create a parameterized job with the following param names
 * BuildProject    => project name from GitHub repository 
 * BuildBranch     => GitHub repository branch for BuildProject
 * SeleniumProject => project name from GitHub repository
 * TestBranch      => GitHub repository branch for SeleniumProject
 * 2) Required Jar files are placed inside [{JENKINS_HOME}\Jars\] directory
 * 3) CredentialsId for GitHub is user defined when configuring GitHub credentials 
*/

pipeline
{
	agent none
	environment
	{
		def mvnHome='C:\\Devops\\apache-maven-3.5.2' 
		def workspace="${JENKINS_HOME}\\workspace"
		def classpath="${JENKINS_HOME}\\workspace\\${SeleniumProject}\\bin;${JENKINS_HOME}\\Jars\\selenium\\*"
		def testServer='C:\\Devops\\apache-tomcat-8.5.23'
	}
	stages
	{
		stage('Preparation')
		{
			steps
			{
				git branch: '${BuildBranch}', 
				credentialsId: 'Git', 
				url: 'https://github.com/vishalvichu/${BuildProject}.git'
				
				emailext from: "svishal621@gmail.com", 
				to: "svishal621@gmail.com",
				recipientProviders: [[$class: "DevelopersRecipientProvider"]], 
				subject: "Build Initiated ${currentBuild.fullDisplayName}", 
				body: "Automated Build started... ${currentBuild.fullDisplayName}"
			}
			post
			{
				success
				{
					echo 'Git Pull Success'
					emailext from: "svishal621@gmail.com", 
					to: "svishal621@gmail.com",
					recipientProviders: [[$class: "DevelopersRecipientProvider"]], 
					subject: "Build Success ${currentBuild.fullDisplayName}", 
					body: "Git pull Success... ${currentBuild.fullDisplayName}"
				}
				failure
				{
					echo 'Git pull failure'
					mail(from: "svishal621@gmail.com", 
				   to: "svishal621@gmail.com", 
				   cc: "svishal621@gmail.com",
				   subject: "FAILURE: ${currentBuild.fullDisplayName}", 
				   body: "Git pull failed :-${currentBuild.fullDisplayName}")
				}
			}
		}
		stage('Build')
		{
			steps
			{
				echo 'Building Application...'
				
				bat(/${mvnHome}\bin\mvn clean install -DskipTests/)
			}
			post
			{
				success
				{
					echo 'Maven Build Success'
					emailext from: "svishal621@gmail.com", 
					to: "svishal621@gmail.com",
					recipientProviders: [[$class: "DevelopersRecipientProvider"]], 
					subject: "Build Success ${currentBuild.fullDisplayName}", 
					body: "Maven Build Success... ${currentBuild.fullDisplayName}"
				}
				failure
				{
					echo 'Maven build failure'
					mail(from: "svishal621@gmail.com", 
				   to: "svishal621@gmail.com", 
				   cc: "svishal621@gmail.com",
				   subject: "FAILURE: ${currentBuild.fullDisplayName}", 
				   body: "Maven Build failed :-${currentBuild.fullDisplayName}")
				}
			}
		}
		stage('Deploy')
		{
			steps
			{
				echo 'deploying'
				bat (/copy ${workspace}\\${JOB_NAME}\\target\\${BuildProject}.war ${testServer}\\webapps\\*/)
			}
			post
			{
				success
				{
					echo 'Deployement Success'
					emailext from: "svishal621@gmail.com", 
					to: "svishal621@gmail.com",
					recipientProviders: [[$class: "DevelopersRecipientProvider"]], 
					subject: "Deployment Success ${currentBuild.fullDisplayName}", 
					body: "Deployment Success... ${currentBuild.fullDisplayName}"
				}
				failure
				{
					echo 'Deployment failure'
					mail(from: "svishal621@gmail.com", 
				   to: "svishal621@gmail.com", 
				   cc: "svishal621@gmail.com",
				   subject: "FAILURE: ${currentBuild.fullDisplayName}", 
				   body: "Deployment failed :-${currentBuild.fullDisplayName}")
				}
			}
		}
		stage('Liquibase')
		{
			steps
			{
				 bat (/${mvnHome}\\bin\\mvn liquibase:update/)
			}
			post
			{
				success
				{
					echo 'Liquibase success'
					emailext from: "svishal621@gmail.com", 
					to: "svishal621@gmail.com",
					recipientProviders: [[$class: "DevelopersRecipientProvider"]], 
					subject: "liquibase Success ${currentBuild.fullDisplayName}", 
					body: "liquibase Success... ${currentBuild.fullDisplayName}"
				}
				failure
				{
					echo 'Liquibase failure'
					mail(from: "svishal621@gmail.com", 
				   to: "svishal621@gmail.com", 
				   cc: "svishal621@gmail.com",
				   subject: "FAILURE: ${currentBuild.fullDisplayName}", 
				   body: "Liquibase failed :-${currentBuild.fullDisplayName}")
				}
			}
		}
		stage('Unit Test')
		{
			steps
			{
				bat(/${mvnHome}\bin\mvn test/)
			}
			post
			{
				success
				{
					echo 'Unit test success'
					emailext from: "svishal621@gmail.com", 
					to: "svishal621@gmail.com",
					recipientProviders: [[$class: "DevelopersRecipientProvider"]], 
					subject: "Unit test Success ${currentBuild.fullDisplayName}", 
					body: "Unit test Success... ${currentBuild.fullDisplayName}"
				}
				failure
				{
					echo 'Unit test failure'
					mail(from: "svishal621@gmail.com", 
				   to: "svishal621@gmail.com", 
				   cc: "svishal621@gmail.com",
				   subject: "FAILURE: ${currentBuild.fullDisplayName}", 
				   body: "Unit test failed :-${currentBuild.fullDisplayName}")
				}
			}
		}
		stage('Selenium')
		{
			steps
			{
				checkout([$class: 'GitSCM', 
							branches: [[name: '*/${TestBranch}']], 
							doGenerateSubmoduleConfigurations: false, 
							extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: "${JENKINS_HOME}\\workspace\\${SeleniumProject}"]], 
							submoduleCfg: [], 
							userRemoteConfigs: [[credentialsId: 'Git', url: 'https://github.com/vishalvichu/${SeleniumProject}.git']]])
							
				withEnv(["classpath=${classpath}"])
				{
					bat (/java org.testng.TestNG ${JENKINS_HOME}\\workspace\\${SeleniumProject}\\testing.xml/)
				}
			}
			post
			{
				success
				{
					echo 'Selenium test success'
					emailext from: "svishal621@gmail.com", 
					to: "svishal621@gmail.com",
					recipientProviders: [[$class: "DevelopersRecipientProvider"]], 
					subject: "Selenium Success ${currentBuild.fullDisplayName}", 
					body: "Selenium Success... ${currentBuild.fullDisplayName}"
				}
				failure
				{
					echo 'Selenium test failure'
					mail(from: "svishal621@gmail.com", 
					to: "svishal621@gmail.com", 
					cc: "svishal621@gmail.com",
					subject: "FAILURE: ${currentBuild.fullDisplayName}", 
					body: "Selenium failed :-${currentBuild.fullDisplayName}")
				}
			}
		}
		stage('SonarQube')
		{
			steps
			{
				echo 'Sonar scanning started'
				bat (/${mvnHome}\\bin\\mvn clean sonar:sonar/)
			}
			post
			{
				success
				{
					echo 'Sonar success'
					emailext from: "svishal621@gmail.com", 
					to: "svishal621@gmail.com",
					recipientProviders: [[$class: "DevelopersRecipientProvider"]], 
					subject: "SonarQube Success ${currentBuild.fullDisplayName}", 
					body: "SonarQube Success... ${currentBuild.fullDisplayName}"
				}
				failure
				{
					echo 'Sonar failure'
					mail(from: "svishal621@gmail.com", 
					to: "svishal621@gmail.com", 
					cc: "svishal621@gmail.com",
					subject: "FAILURE: ${currentBuild.fullDisplayName}", 
					body: "SonarQube failed :-${currentBuild.fullDisplayName}")
				}
			}
		}
	}
}
