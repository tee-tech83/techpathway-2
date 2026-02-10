pipeline {
  agent any

  environment {
    AWS_REGION = "us-east-1"
    ACCOUNT_ID = "488446293521"

    FRONT_REPO = "${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/tech2-frontend"
    BACK_REPO  = "${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/tech2-backend"

    ECS_CLUSTER   = "techchallenge2-cluster"
    FRONT_SERVICE = "techchallenge2-frontend-svc"
    BACK_SERVICE  = "techchallenge2-backend-svc"
  }

  stages {
    stage("Checkout") {
      steps { checkout scm }
    }

    stage("Build Images") {
  steps {
    sh '''
      echo "==== WHERE AM I? ===="
      pwd
      ls -la
      echo "==== FRONTEND FOLDER ===="
      ls -la frontend || true
      echo "==== BACKEND FOLDER ===="
      ls -la backend || true
      echo "======================="

      GIT_SHA=$(git rev-parse --short HEAD)

      docker build -t tech2-frontend:$GIT_SHA ./frontend
      docker build -t tech2-backend:$GIT_SHA ./backend
    '''
  }
}

    stage("Login to ECR") {
      steps {
        sh '''
          aws ecr get-login-password --region $AWS_REGION \
            | docker login --username AWS --password-stdin $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
        '''
      }
    }

    stage("Tag & Push") {
      steps {
        sh '''
          GIT_SHA=$(git rev-parse --short HEAD)

          docker tag tech2-frontend:$GIT_SHA $FRONT_REPO:$GIT_SHA
          docker tag tech2-backend:$GIT_SHA  $BACK_REPO:$GIT_SHA

          docker push $FRONT_REPO:$GIT_SHA
          docker push $BACK_REPO:$GIT_SHA
        '''
      }
    }

    stage("Deploy to ECS") {
      steps {
        sh '''
          aws ecs update-service --cluster $ECS_CLUSTER --service $FRONT_SERVICE --force-new-deployment --region $AWS_REGION
          aws ecs update-service --cluster $ECS_CLUSTER --service $BACK_SERVICE  --force-new-deployment --region $AWS_REGION
        '''
      }
    }
  }
}
