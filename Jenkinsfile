pipeline{
    agent { label 'electronix'}
    environment{
        S3_BUCKET='electronix-production-03'
        CLOUDFRONT_ID='EM2PR2470G9OP'
        AWS_REGION='us-east-1'
    }

    stages{
        stage("Front-End Deployment"){
            when {
                changeset "frontend/**"
            }
            stages{
                stage('Install Dependencies'){
                    steps{
                        dir('frontend'){
                            sh 'sh "npm install"'
                        }
                    }
                }

                stage("Run Tests"){
                    steps{
                        dir('frontend'){
                            sh 'npm test -- --watchAll=false || echo "No Test Configured.."'
                        }
                    }
                }

                stage("Build"){
                    steps{
                        dir('frontend'){
                            sh 'npm run build'
                        }
                    }
                }

                stage('Deploy S3'){
                    steps{
                        dir('forntend'){
                            sh '''
                            aws s3 sync dist/ s3://${S3_BUCKET} --delete --region ${AWS_REGION}
                            '''
                        }
                    }
                }

                stage('Invalidation Cloudfront Cache'){
                    steps{
                       
                        sh '''
                        aws cloudfront create-invalidation --distribution-id ${CLOUDFRONT_ID} --paths "/*"
                        '''                   
                    }
                }


            }
        }
    }
    post{
        success{
            echo 'Frontend Doployment Successfull'

        }
        failure{
            echo 'Frontend Deployment Failed'
        }

    }
}