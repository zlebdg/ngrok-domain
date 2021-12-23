properties([
        [$class              : 'ParametersDefinitionProperty',
         parameterDefinitions: [[$class      : 'StringParameterDefinition',
                                 defaultValue: '',
                                 description : '',
                                 name        : 'DOMAIN',
                                ]]]
])

node("win10") {
    stage("Step 1 - checkout code") {
        var_of_git = checkout(scm)
        writeFile(file: 'a.txt', text: 'haha')
    }
}