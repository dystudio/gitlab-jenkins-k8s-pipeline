// 镜像仓库地址
def registry = "docker.io"
// 用户
def docker_user = "wangzan18"
// 镜像仓库项目
def project = "gitlab-pipeline"
// 镜像名称
def app_name = "citest"
// 镜像完整名称
def image_name = "${registry}/${docker_user}/${project}:${app_name}-${BUILD_NUMBER}"
// git仓库地址
def git_address = "http://gitlab.wzlinux.com/root/gitlab-pipeline.git"
// fenzhi分支
def branch = "*/master"

// 认证
def dockerhub_auth = "fcf11c53-7969-4654-a609-141f6908e348"
def gitlab_auth = "cca83969-0fe3-4aa8-9c37-172f19d7338f"

podTemplate(
    label: 'jenkins-agent', 
    cloud: 'kubernetes', 
    containers: [
       containerTemplate(name: 'jnlp', image: "wangzan18/jenkins-agent:maven-3.6.3"),
       containerTemplate(name: 'docker', image: 'docker:19.03.1-dind', ttyEnabled: true, command: 'cat')
    ],
    volumes: [
        hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock')
    ]){
    node('jenkins-agent'){
        stage('拉取代码') { // for display purposes
            checkout([$class: 'GitSCM',branches: [[name: '*/master']], userRemoteConfigs: [[credentialsId: "${gitlab_auth}", url: "${git_address}"]]])
        }
        stage('代码编译') {
        //    sh "mvn clean package -Dmaven.test.skip=true"
            sh "ls"
        }
        stage('构建镜像') {
            container('docker') {
                stage('打包镜像') {
                   withCredentials([usernamePassword(credentialsId: "${dockerhub_auth}", passwordVariable: 'password', usernameVariable: 'username')]) {
                   sh """
                    docker build -t ${image_name} .
                    docker login -u ${username} -p '${password}'
                    docker push ${image_name}
                    """
                    }
                }  
            }    
        }
    }
}