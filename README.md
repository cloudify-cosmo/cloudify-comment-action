Parse Pull Requests comments and trigger relevant actions on Cloudify.

# Environment Variables

This Action uses the Cloudify Profile environment variables described in the official
Cloudify documentation (see [More Information](#more-information) below).

# Inputs

(Certain commonly-used inputs are documented in our official website; see [More Information](#more-information) below)

| Name | Description
|------|------------
| `comment` | comment to parse and take action based on
| `github_token` | github token that will be used to add comment to PR -you will get it from actions secrets-
| -----------------| The following inputs is related to terraform uses cases -you can ignore with non-terraform comments-
| `module-file` | URL/path to Terraform module archive
| `module-source-path` | path to Terraform module inside the archive
| `variables-file` | YAML/JSON file containing template variables
| `environment-file` | YAML/JSON file containing environment variables to pass to the Terraform process
| `environment-mapping` | A whitespace-delimited list of strings denoting environment variables to pass through (see below)

# Outputs

| Name | Description
|------|------------
| `result` | Comment handling result

## Notes

* to activate the comment part correctly , the workflow should be defined on your default branch -bellow an example how to use it-
* this action can trigger any command that you can execute using cloudify-cosmo/cli-action which interface with `cfy` cli command
* this action handles terraform comments [`cfy terraform plan`, `cfy terraform apply`, `cfy terraform destroy`] which triggers the following actions respectively [cloudify-cosmo/terraform-plan-action, cloudify-cosmo/terraform-apply-action, cloudify-cosmo/terraform-destroy-action] , it will checkout the head branch that you opened a Poll Request from if you don't pass `module-file` value

# Example

```yaml
name: execute cloudify command from PR comment
on:
  issue_comment:
    types: [created]
jobs:
  handle-comment:
    runs-on: ubuntu-latest
    steps:
      - name: handle comment on PR
        id: handle_comment
        uses: cloudify-cosmo/cloudify-comment-action@v1.2
        with:
          comment: "${{ github.event.comment.body }}"
          github_token: "${{ secrets.GITHUB_TOKEN }}" # This token is provided by Actions, you do not need to create your own token
          # the following variables for terraform use-cases
          environment-name: "test-terraform-$GITHUB_RUN_ID"
          labels: test_label:just_for_fun
          module-source-path: template/modules/public_vm
          variables-file: variables.yaml
```

# More Information

Refer to [Cloudify CLI Documentation](https://docs.cloudify.co/latest/cli/) for additional information about `cfy` cli command line.

Refer to [Cloudify CI/CD Integration](https://docs.cloudify.co/latest/working_with/integration/) for additional information about
Cloudify's integration with CI/CD tools.
