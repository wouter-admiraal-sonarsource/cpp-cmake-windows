node {
  stage('SCM') {
    checkout scm
  }
  stage('Download Build Wrapper') {
    powershell "$path = \"$HOME/.sonar/build-wrapper-win-x86.zip\""
    powershell "mkdir $HOME/.sonar"
    powershell "[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12"
    powershell "(New-Object System.Net.WebClient).DownloadFile(\"http://10.42.0.1:9000/static/cpp/build-wrapper-win-x86.zip\", $path)"
    powershell "Add-Type -AssemblyName System.IO.Compression.FileSystem"
    powershell "[System.IO.Compression.ZipFile]::ExtractToDirectory($path, \"$HOME/.sonar\")"
  }
  stage('Build') {
    powershell "$path = \"$HOME/.sonar/sonar-scanner-cli-4.6.2.2472-windows.zip\""
    powershell "[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12"
    powershell "(New-Object System.Net.WebClient).DownloadFile(\"https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.6.2.2472-windows.zip\", $path)"
    powershell "Add-Type -AssemblyName System.IO.Compression.FileSystem"
    powershell "[System.IO.Compression.ZipFile]::ExtractToDirectory($path, \"$HOME/.sonar\")"
  }
  stage('SonarQube Analysis') {
    def scannerHome = tool 'SonarScanner';
    withSonarQubeEnv() {
      powershell "${scannerHome}/bin/sonar-scanner -Dsonar.cfamily.build-wrapper-output=.sonar/bw-output"
    }
  }
}
