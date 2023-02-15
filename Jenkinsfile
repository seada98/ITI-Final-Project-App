pipeline {
    agent any

    stages {

        stage('CI'){
            steps {

                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')])            {

                sh """
                    cd App
                    docker build . -t seada98/python-app:v$BUILD_NUMBER
                    docker login -u ${USERNAME} -p ${PASSWORD}
                    docker push seada98/python-app:v$BUILD_NUMBER
                    cd ..
                """
                }
              }
        }
    
        stage('CD'){
            steps {

                withCredentials([file(credentialsId: 'accesscluster', variable: 'config')]){
                    sh """
                        gcloud auth activate-service-account --key-file=${config}
                        gcloud container clusters get-credentials my-private-cluster --zone us-central1-c --project iti-seada
                        sed -i 's/tag/${BUILD_NUMBER}/g' Deployment/python-deployment.yaml
                        kubectl apply -Rf Deployment
                    """
                }
            }
 
        }
    }    
}
