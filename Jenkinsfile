pipeline {
    agent any
    
    environment {
        PM2 = "C:\\Users\\ernests\\AppData\\Roaming\\npm\\pm2.cmd"
        HOME = "C:\\Users\\ernests"
        HOMEDRIVE = "C:"
        HOMEPATH = "\\Users\\ernests"
    }

    stages {
        stage('install-pip-deps') {
            steps { script { installPythonDeps() } }
        }
        stage('deploy-to-dev') {
            steps { script { deployEnv('dev', '7001') } }
        }
        stage('tests-on-dev') {
            steps { script { runTests('dev') } }
        }
        stage('deploy-to-stg') {
            steps { script { deployEnv('stg', '7002') } }
        }
        stage('tests-on-stg') {
            steps { script { runTests('stg') } }
        }
        stage('deploy-to-preprod') {
            steps { script { deployEnv('preprod', '7003') } }
        }
        stage('tests-on-preprod') {
            steps { script { runTests('preprod') } }
        }
        stage('deploy-to-prod') {
            steps { script { deployEnv('prod', '7004') } }
        }
        stage('tests-on-prod') {
            steps { script { runTests('prod') } }
        }
    }
}

def installPythonDeps() {
    echo "Instalējam bibliotēkas.."
    dir('python-app-main') {
        git url: 'https://github.com/mtararujs/python-greetings', branch: 'main'
        bat 'dir'
        bat "python -m venv venv && .\\venv\\Scripts\\python -m pip install -r requirements.txt"
    }
}

def deployEnv(envName, port) {
    echo "Izvietojam ${envName} uz portu ${port}.."
    dir("app-${envName}") {
        git url: 'https://github.com/mtararujs/python-greetings', branch: 'main'
        bat "\"${PM2}\" delete greetings-app-${envName} || cmd /c \"exit /b 0\""
        def venvPath = "${WORKSPACE}\\python-app-main\\venv\\Scripts\\python.exe"
        bat "\"${PM2}\" start app.py --name greetings-app-${envName} --interpreter \"${venvPath}\" -- --port ${port}"
    }
}

def runTests(envName) {
    echo "API testi videi ${envName}.."
    dir("tests-${envName}") {
        git url: 'https://github.com/mtararujs/course-js-api-framework', branch: 'main'
        bat "npm install && npm run greetings greetings_${envName}"
    }
}