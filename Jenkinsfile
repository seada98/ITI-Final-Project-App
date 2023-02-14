pipeline {
    agent any

    stages {

        stage('CI'){
            steps {

                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')])            {

                sh """
                    docker build . -t seada98/python-app
                    docker login -u ${USERNAME} -p ${PASSWORD}
                    docker push seada98/python-app
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
                        kubectl apply -Rf Deployment
                    """
                }
            }
 
        }
    }    
}
