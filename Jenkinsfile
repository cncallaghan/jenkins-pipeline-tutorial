// Declarative pipelines must be enclosed with a "pipeline" directive.
pipeline {
    // This line is required for declarative pipelines. Just keep it here.
    agent any

    // This section contains environment variables which are available for use in the
    // pipeline's stages.
    environment {
	    region = "us-east-1"
        docker_repo_uri = "894427396428.dkr.ecr.us-east-1.amazonaws.com/cnc-sample-app"
		task_def_arn = ""
        cluster = ""
        exec_role_arn = ""
    }
    
    // Here you can define one or more stages for your pipeline.
    // Each stage can execute one or more steps.
    stages {
        // This is a stage.
        stage('Build') {
    steps {
        // Get SHA1 of current commit
        script {
            commit_id = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
        }
        // Build the Docker image
        sh "docker build -t ${docker_repo_uri}:${commit_id} ."
        // Get Docker login credentials for ECR
        sh "aws ecr get-login --no-include-email --region ${region} | sh"
        // Push Docker image
        sh "docker push ${docker_repo_uri}:${commit_id}"
        // Clean up
        sh "docker rmi -f ${docker_repo_uri}:${commit_id}"
        // THIS IS A STUPID TEST, I HOPE THIS ISN't HOW IT WILL WORK... EDIT: this is how it works...
        sh "latest_image=\$(aws ecr describe-images --output json --repository-name cnc-sample-app --region us-east-1 --query 'sort_by(imageDetails,& imagePushedAt)[-1].imageTags[0]' | sed 's/\"//g'); revision_number=\$(aws ecs register-task-definition --family cnc-task-definition2 --task-role-arn arn:aws:iam::894427396428:role/cnc-ecs-role2 --execution-role-arn arn:aws:iam::894427396428:role/cnc-ecs-role2 --network-mode awsvpc --requires-compatibilities FARGATE --cpu 256 --memory 512 --region us-east-1 --container-definitions  \"[{\\\"name\\\":\\\"cnc-container1\\\", \\\"image\\\":\\\"894427396428.dkr.ecr.us-east-1.amazonaws.com/cnc-sample-app:\$latest_image\\\",\\\"cpu\\\":0,\\\" essential\\\":true,\\\"portMappings\\\":[{\\\"protocol\\\":\\\"tcp\\\",\\\"containerPort\\\":80,\\\"hostPort\\\":80}]}]\" | grep revision | awk '{print \$2}'); aws ecs update-service --cluster arn:aws:ecs:us-east-1:894427396428:cluster/cnc-cluster2 --service cnc-service6 --task-definition arn:aws:ecs:us-east-1:894427396428:task-definition/cnc-task-definition2:\$revision_number --region us-east-1; declare -i revision_number; old_revision=\$((\$revision_number-1)); aws ecs deregister-task-definition --task-definition cnc-task-definition2:\$old_revision --region us-east-1"
        // sleep for 10s (NEED TO REPLACE THIS WITH CONDITIONAL WAIT)
        // sh "sleep 10"
        // GET call for the most recent ECR image tag, save it as a variable called $latest_image
        // sh "latest_image=\$(aws ecr describe-images --output json --repository-name cnc-sample-app --region us-east-1 --query 'sort_by(imageDetails,& imagePushedAt)[-1].imageTags[0]' | sed 's/\"//g')"
        // create new task-definition revision with updated ECR image tag, store revision number as $revision_number
        // sh "sleep 10"
        // same call but without backslashes: aws ecs register-task-definition --family cnc-task-definition-cli-test --task-role-arn arn:aws:iam::894427396428:role/cnc-ecs-role2 --execution-role-arn arn:aws:iam::894427396428:role/cnc-ecs-role2 --network-mode awsvpc --requires-compatibilities FARGATE --cpu 256 --memory 512 --region us-east-1 --container-definitions  "[{\"name\":\"cnc-container-cli-test\",\"image\":\"894427396428.dkr.ecr.us-east-1.amazonaws.com/cnc-sample-app:$latest_image\",\"cpu\":0,\"essential\":true,\"portMappings\":[{\"protocol\":\"tcp\",\"containerPort\":80,\"hostPort\":80}]}]"
        // sh "revision_number=\$(aws ecs register-task-definition --family cnc-task-definition2 --task-role-arn arn:aws:iam::894427396428:role/cnc-ecs-role2 --execution-role-arn arn:aws:iam::894427396428:role/cnc-ecs-role2 --network-mode awsvpc --requires-compatibilities FARGATE --cpu 256 --memory 512 --region us-east-1 --container-definitions  \"[{\\\"name\\\":\\\"cnc-container1\\\",\\\"image\\\":\\\"894427396428.dkr.ecr.us-east-1.amazonaws.com/cnc-sample-app:\$latest_image\\\",\\\"cpu\\\":0,\\\"essential\\\":true,\\\"portMappings\\\":[{\\\"protocol\\\":\\\"tcp\\\",\\\"containerPort\\\":80,\\\"hostPort\\\":80}]}]\" | grep revision | awk '{print \$2}')"
        // sleep another 10 seconds (NEED TO REPLACE THIS WITH CONDITIONAL WAIT)
        // sh "sleep 10"
        // update the cluster's service to use the new task definition
        // sh "aws ecs update-service --cluster arn:aws:ecs:us-east-1:894427396428:cluster/cnc-cluster2 --service cnc-service6 --task-definition arn:aws:ecs:us-east-1:894427396428:task-definition/cnc-task-definition2:\$revision_number --region us-east-1"
        // would like to get this working:
        // ; aws ecs stop-task --cluster cnc-cluster2 --task arn:??????????????????????????? --region us-east-1
        //to solve the above ARN here are the commands to be run at the end of the one-line leviathan above:
        // aws ecs list-tasks --cluster cnc-cluster2 --service-name cnc-service6 --region us-east-1 | awk '/cnc-cluster2/ {print}' | sed 's/,$//' | awk '{ sub(/^[ \t]+/, ""); print }' | awk '{printf "%s ",$0} END {print ""}'
    }
}
    }
}
