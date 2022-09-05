## AWS ECS "Set Container Name" Action for GitHub Actions

Updates the container name in an Amazon ECS task definition JSON file, creating a new task definition file.

**Table of Contents**

<!-- toc -->

- [Usage](#usage)
- [License Summary](#license-summary)
- [Security Disclosures](#security-disclosures)

<!-- tocstop -->

## Usage Example

To update the container name to `database` for the `postgres` container in the task definition file,
and then deploy the edited task definition file to ECS:

```yaml
- name: Modify AWS ECS task definition for postgres container
  id: render-database-container
  uses: ohueter/aws-ecs-task-definition-set-container-name@v1
  with:
    task-definition: task-definition.json
    container-name: postgres
    new-container-name: database

- name: Deploy to AWS ECS service
  uses: aws-actions/amazon-ecs-deploy-task-definition@v1
  with:
    task-definition: ${{ steps.render-database-container.outputs.task-definition }}
    service: my-service
    cluster: my-cluster
```

If your task definition file holds multiple containers in the `containerDefinitions`
section which require updated container names, chain multiple executions of this action
together using the output value from the first action for the `task-definition`
input of the second:

```yaml
- name: Modify AWS ECS task definition for first container
  id: render-database-container
  uses: ohueter/aws-ecs-task-definition-set-container-name@v1
  with:
    task-definition: task-definition.json
    container-name: postgres
    new-container-name: database

- name: Modify AWS ECS task definition for second container
  id: render-app-container
  uses: ohueter/aws-ecs-task-definition-set-container-name@v1
  with:
    task-definition: ${{ steps.render-database-container.outputs.task-definition }}
    container-name: web
    new-container-name: app

- name: Deploy to AWS ECS service
  uses: aws-actions/amazon-ecs-deploy-task-definition@v1
  with:
    task-definition: ${{ steps.render-app-container.outputs.task-definition }}
    service: my-service
    cluster: my-cluster
```

See [action.yml](action.yml) for the full documentation for this action's inputs and outputs.

## License Summary

This code is made available under the MIT license. It has been forked from https://github.com/aws-actions/amazon-ecs-render-task-definition/.
