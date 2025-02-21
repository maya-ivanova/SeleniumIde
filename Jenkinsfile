node {
    stage('Set Environment Variables') {
        env.CHROME_VERSION = "133.0.6943.98"
        env.CHROMEDRIVER_VERSION = "133.0.6943.98"
        env.CHROME_PATH = "C:\\Users\\jenkins\\AppData\\Local\\Jenkins\\.jenkins\\workspace\\SeleniumIde"
        env.CHROMEDRIVER_PATH = "C:\\Users\\jenkins\\AppData\\Local\\Jenkins\\.jenkins\\workspace\\SeleniumIde"
    }
    
    stage('Checkout Code') {
        cleanWs()
        git branch: 'master', url: 'https://github.com/maya-ivanova/SeleniumIde.git'
    }

    stage('Set up .NET Core') {
        bat '''
        echo Installing .NET SDK 6.0
        choco install dotnet-sdk -y --version=6.0.100
        '''
    }

    stage('Install Chrome v.133.0.6943.98') {
        bat '''
        echo Installing Google Chrome version %CHROME_VERSION%
        choco install googlechrome --version=%CHROME_VERSION% -y --allow-downgrade --ignore-checksums
        '''        
    }

    stage('Download and Install ChromeDriver') { 
        bat ''' 
        echo Downloading ChromeDriver version %CHROMEDRIVER_VERSION%
        powershell -command "Invoke-WebRequest -Uri https://storage.googleapis.com/chrome-for-testing-public/133.0.6943.98/win64/chromedriver-win64.zip -OutFile chromedriver-win64.zip -UseBasicParsing"
        powershell -command "Expand-Archive -Path chromedriver-win64.zip -DestinationPath ."
        powershell -command "Move-Item -Path .\\chromedriver.exe -Destination '%CHROME_INSTALL_PATH%\\chromedriver.exe' -Force"
        ''' 
    }

    stage('Restore NuGet Dependencies') {
        bat 'dotnet restore SeleniumIde.sln'
    }

    stage('Build Solution') {
        bat 'dotnet build SeleniumIde.sln --configuration Release'
    }

    stage('Run Tests') {
        bat 'dotnet test SeleniumIde.sln --configuration Release --logger "trx;LogFileName=TestResults.trx"'
    }
}

post {
    always {
        archiveArtifacts artifacts: '**/TestResults/*.trx', allowEmptyArchive: true
        junit '**/TestResults/*.trx'
    }
}
