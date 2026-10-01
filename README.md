# loan-api-lab-infra

Terraform for the **loan-api-lab** incident exercise (<https://github.com/eddiez0719/loan-api-lab>).
It builds the "production" environment in AWS: ECR, ECS Fargate cluster and service, an Application Load Balancer (the `<PROD_ALB_DNS>`), and a GitHub OIDC deploy role (no long-lived AWS keys).

> **Cost:** an ALB and a running Fargate task bill by the hour. Only `apply` during the lesson and **always run `terraform destroy` afterwards.**

## Use
1. Fork `loan-api-lab` to your own GitHub account.
2. `terraform init`
3. `terraform apply -var github_repo=<your-user>/loan-api-lab`
   - If your AWS account already has the GitHub OIDC provider, add `-var create_github_oidc_provider=false`.
4. In your fork, create the GitHub Environment **production** and add the variables printed by the `github_environment_variables` output (`AWS_REGION`, `AWS_ROLE_ARN`, `ECR_REPOSITORY`, `ECS_CLUSTER`, `ECS_SERVICE`, `BASE_URL`).
5. In your fork's `.aws/task-definition.json`, replace `<ACCOUNT_ID>` with your 12-digit AWS account id (or paste the `task_execution_role_arn` output).
6. Run the `loan-api CI/CD` workflow (Actions → Run workflow). The first deploy replaces the bootstrap placeholder.
7. `curl http://<prod_alb_dns>/version` — you are now ready for LAB-1.

## Clean up
`terraform destroy -var github_repo=<your-user>/loan-api-lab` (repeat any `-var` flags you used for apply).

## Notes
- The ECS service ignores `task_definition` drift on purpose: after bootstrap, the pipeline owns deployments. Do not edit ECS or task definitions in the console during the lab.
- State is local by default. Do not commit `*.tfstate`.
