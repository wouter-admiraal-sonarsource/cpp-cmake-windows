node {
  stage('SCM') {
    checkout scm
  }
  stage('Download Build Wrapper') {
    powershell '''
      $path = "$HOME/.sonar/build-wrapper-win-x86.zip"
      rm build-wrapper-win-x86 -Recurse -Force -ErrorAction SilentlyContinue
      rm $path -Force -ErrorAction SilentlyContinue
      mkdir $HOME/.sonar
      [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
      (New-Object System.Net.WebClient).DownloadFile("http://10.42.0.1:9000/static/cpp/build-wrapper-win-x86.zip", $path)
      Add-Type -AssemblyName System.IO.Compression.FileSystem
      [System.IO.Compression.ZipFile]::ExtractToDirectory($path, "$HOME/.sonar")
    '''
  }
  stage('Build') {
    powershell '''
      $env:Path += ";$HOME/.sonar/build-wrapper-win-x86"
      rm build -Recurse -Force -ErrorAction SilentlyContinue
      mkdir build
      cd build
      cmake ..
      cd ..
      build-wrapper-win-x86-64 --out-dir bw-output cmake --build build/ --config Release
    '''
  }
  stage('SonarQube Analysis') {
    def scannerHome = tool 'SonarScanner';
    withSonarQubeEnv() {
      powershell "${scannerHome}/bin/sonar-scanner -Dsonar.cfamily.build-wrapper-output=.sonar/bw-output"
    }
  }
}
