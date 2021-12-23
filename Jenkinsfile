properties([
        [$class              : 'ParametersDefinitionProperty',
         parameterDefinitions: [[$class      : 'StringParameterDefinition',
                                 defaultValue: '',
                                 description : '',
                                 name        : 'DOMAIN',
                                ]]],
        disableConcurrentBuilds(),
])

node("win10") {
    stage("Step 0 - clean up") {
        try {
            powershell('''
                remove-item -force -recurse temp
            ''')
        } catch (e) {

        }
    }
    stage("Step 1 - checkout code") {
        dir("temp") {
            var_of_git = checkout(scm)
            powershell('''
                git fetch -a
                git fetch origin main
                git checkout main
            ''')
        }
    }
    stage("Step 2 - push new commit") {
        dir("temp") {
            latestDomain = readFile(file: 'domain.txt').trim()

            echo latestDomain
            echo params.DOMAIN

            if (latestDomain.contains(params.DOMAIN)) {
                echo 'The domain has no change - ' + params.DOMAIN
            } else {
                env.DOMAIN = params.DOMAIN
                writeFile(file: 'domain.txt', text: params.DOMAIN)
                withCredentials([usernamePassword(credentialsId: 'github-password-for-xuqplus', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                    env.TEMP_GIT_URL = var_of_git.get("GIT_URL").replace("https://", "https://" + env.GIT_USERNAME + ":" + env.GIT_PASSWORD + "@")

                    powershell('''
                        git config user.name jenkins
                        git config user.email jenkins@none.io
                        git add .
                        git commit -m "$env.BUILD_NUMBER - Change the domain to $env:DOMAIN"
                        git remote set-url origin $env:TEMP_GIT_URL
                        git push origin main -f
                    ''')
                }
            }
        }
    }
}