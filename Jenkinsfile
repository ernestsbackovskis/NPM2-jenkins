pipeline {
    agent any

    stages {
        stage('install-pip-deps') {
            steps {
                script { installPythonDeps() }
            }
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

// --- FUNKCIJAS (PRASĪBA: LABĀ PRAKSE - 2 PUNKTI) ---

def installPythonDeps() {
    echo "Solis: install-pip-deps. Instalējam bibliotēkas.."
    dir('python-app-main') {
        // Klonējam repozitoriju
        git url: 'https://github.com/mtararujs/python-greetings', branch: 'main'
        
        // Pārbaudām failus ar 'dir' (Windows ekvivalents 'ls')
        bat 'dir'
        
        // Veidojam venv un instalējam requirements
        bat """
            python -m venv venv
            .\\venv\\Scripts\\python -m pip install -r requirements.txt
        """
    }
}

def deployEnv(envName, port) {
    echo "Solis: deploy-to-${envName}. Izvietojam uz portu ${port}.."
    dir("app-${envName}") {
        git url: 'https://github.com/mtararujs/python-greetings', branch: 'main'
        
        // Apstādinām esošo servisu (ignorējot kļūdu, ja tāda nav)
        bat "pm2 delete greetings-app-${envName} || set errorlevel=0"
        
        // Palaižam izmantojot venv interpretatoru no pirmā soļa
        def venvPath = "${WORKSPACE}\\python-app-main\\venv\\Scripts\\python.exe"
        bat "pm2 start app.py --name greetings-app-${envName} --interpreter \"${venvPath}\" -- --port ${port}"
    }
}

def runTests(envName) {
    echo "Solis: tests-on-${envName}. Palaižam API testus.."
    dir("tests-${envName}") {
        git url: 'https://github.com/mtararujs/course-js-api-framework', branch: 'main'
        bat "npm install"
        bat "npm run greetings greetings_${envName}"
    }
}