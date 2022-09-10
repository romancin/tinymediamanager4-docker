registry="romancin/tinymediamanager"

podTemplate(label: 'github-docker-builder', cloud: 'kubernetes',
  containers: [
    containerTemplate(name: 'buildkit', image: 'moby/buildkit:master', ttyEnabled: true, privileged: true),
    //containerTemplate(name: 'docker-readme', image: 'sheogorath/readme-to-dockerhub:latest', ttyEnabled: true, privileged: true),
  ],
  volumes: [
    secretVolume(secretName: 'docker-config', mountPath: '/root/.docker')
  ]) {
       node('github-docker-builder') {
         stage('Cloning Git Repository') {
             container('buildkit') {
               git url: 'https://github.com/romancin/tinymediamanager4-docker.git',
               branch: '$BRANCH_NAME'
             }
         }
         stage('Building image and pushing it to the registry (develop)') {
           if (env.BRANCH_NAME == 'develop') {
             def gitbranch = sh(returnStdout: true, script: 'git rev-parse --abbrev-ref HEAD').trim()
             def version = readFile('VERSION')
             def versions = version.split('\\.')
             def major = gitbranch + '-' + versions[0]
             def minor = gitbranch + '-' + versions[0] + '.' + versions[1]
             def patch = gitbranch + '-' + version.trim()
             container('buildkit') {
                 sh """
                      buildctl build --frontend dockerfile.v0 --local context=. --local dockerfile=. --output type=image,name=${registry}:${gitbranch}-v4,push=true
                      buildctl build --frontend dockerfile.v0 --local context=. --local dockerfile=. --output type=image,name=${registry}:${major},push=true
                      buildctl build --frontend dockerfile.v0 --local context=. --local dockerfile=. --output type=image,name=${registry}:${minor},push=true
                      buildctl build --frontend dockerfile.v0 --local context=. --local dockerfile=. --output type=image,name=${registry}:${patch},push=true
                    """
             }
           }
         }
         stage('Building image and pushing it to the registry (main)') {
           if (env.BRANCH_NAME == 'main') {
             def gitbranch = sh(returnStdout: true, script: 'git rev-parse --abbrev-ref HEAD').trim()
             def version = readFile('VERSION')
             def versions = version.split('\\.')
             def major = gitbranch + '-' + versions[0]
             def minor = gitbranch + '-' + versions[0] + '.' + versions[1]
             def patch = gitbranch + '-' + version.trim()
             container('buildkit') {
                 sh """
                      buildctl build --frontend dockerfile.v0 --local context=. --local dockerfile=. --output type=image,name=${registry}:latest-v4,push=true
                      buildctl build --frontend dockerfile.v0 --local context=. --local dockerfile=. --output type=image,name=${registry}:${major},push=true
                      buildctl build --frontend dockerfile.v0 --local context=. --local dockerfile=. --output type=image,name=${registry}:${minor},push=true
                      buildctl build --frontend dockerfile.v0 --local context=. --local dockerfile=. --output type=image,name=${registry}:${patch},push=true
                    """
             }
           }
         }
       }
}

properties([[
    $class: 'BuildDiscarderProperty',
    strategy: [
        $class: 'LogRotator',
        artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10']
    ]
]);
