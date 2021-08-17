pipeline {

	// More info about the Declarative Pipeline Syntax on https://www.jfrog.com/confluence/display/JFROG/Declarative+Pipeline+Syntax
	// Declarative syntax is available from version 3.0.0 of the Jenkins Artifactory Plugin.

	agent any

	

	
	stages {
		
		stage ("Clone") {
			steps {
				git branch: "master",
				url: "https://github.com/sverdlov93/ide-plugins-common"
				// credentialsId: 'git_cred_id' (If cloning the code requires credentials, set credentials to artifactory server assined in Jenkins > Configure System > credentials > "username with password" > ID: "git-cred-id" )
			}
		}

		stage ("Artifactory configuration") {
			steps {
				rtServer (
					id: "ci-setup-cmd",
					url: "https://michaelsv.jfrog.io/artifactory/",
					credentialsId: 'rt-cred-id', // (Credentials to artifactory server assined in Jenkins > Configure System > credentials > "username with password" > ID: "rt-cred-id" )

 					// bypassProxy: true, (If Jenkins is configured to use an http proxy, you can bypass the proxy when using this Artifactory server)
					// timeout: 300 , (Configure the connection timeout (in seconds). The default value (if not configured) is 300 seconds)
				)
				rtGradleDeployer (
					id: "GRADLE_DEPLOYER",
					serverId: "ci-setup-cmd",
					repo: "gradle-virtual",

					// threads: 6, (Optional - Attach custom properties to the published artifacts)
					// properties: ['key1=value1', 'key2=value2'], (Optional - Attach custom properties to the published artifacts)
				)
				rtGradleResolver (
					id: "GRADLE_RESOLVER",
					serverId: "ci-setup-cmd",
					repo: "gradle-local"
				)
			}
		}

		stage ("Exec Gradle") {
			steps {
				rtGradleRun (
					buildFile: 'build.gradle',
					tasks: "clean artifactoryPublish",
					rootDir: "",
					resolverId: "GRADLE_RESOLVER",
					deployerId: "GRADLE_DEPLOYER",

					// tool: {build installation name}, // Jenkins > Gradle jenkins > Global Tool Configuration > Gradle installations
					// threads: 6, (Optional - Attach custom properties to the published artifacts)
					// properties: ['key1=value1', 'key2=value2'], (Optional - Attach custom properties to the published artifacts)
					// buildName: 'my-build-name', (If the build name and build number are not set here, the current job name and number will be used:)
					// buildNumber: '17',
					// project: 'my-project-key' (Optional - Only if this build is associated with a project in Artifactory, set the project key as follows.)
				)
			}
		}

		stage ("Config build info") {
			steps {
				rtBuildInfo (
					captureEnv: true,
					includeEnvPatterns: ["*"],

  					// excludeEnvPatterns: ['*private*', 'internal-*'],
					// buildName: 'my-build-name', (If the build name and build number are not set here, the current job name and number will be used:)
					// buildNumber: '17',
					// project: 'my-project-key' (Optional - Only if this build is associated with a project in Artifactory, set the project key as follows.)
				)
			}
		}

		stage ("Publish build info") {
			steps {
				rtPublishBuildInfo (
					serverId: "ci-setup-cmd",

					// buildName: 'my-build-name', (If the build name and build number are not set here, the current job name and number will be used:)
					// buildNumber: '17',
					// project: 'my-project-key' (Optional - Only if this build is associated with a project in Artifactory, set the project key as follows.)
				)
			}
		}

	}
}
