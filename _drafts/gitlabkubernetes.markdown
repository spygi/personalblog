## Autodevops in Gitlab
Beta https://docs.gitlab.com/ee/topics/autodevops/index.html#prerequisites
Fails on production ensure namespace with authentication error: Unable to connect to the server: x509: certificate signed by unknown authority

## Kubernetes side
+ Create a cluster
+ Find endpoint IP ()
+ Get secret: Config -> default_token_something from default namespace->get yaml

## Gitlab side
+ Project -> settings -> CI/CD: Enable auto devops (The Auto DevOps pipeline configuration will be used when there is no .gitlab-ci.yml in the project.) Specify domain for auto deploy: nes-backend.spygi.me
+ CI/CD -> Kubernetes -> Cluster
API from endpoint according to https://gitlab.com/gitlab-org/gitlab-ce/issues/39177#note_47112081 and https://docs.gitlab.com/ee/ci/autodeploy/quick_start_guide.html (tried https://container.googleapis.com as well)
CA certificate and token from default_token_something, make sure to add START and END, remove new lines etc. Same for token.

according to this https://gitlab.com/gitlab-org/gitlab-ce/issues/39177#note_46822173
and https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/#config linked from https://docs.gitlab.com/ce/user/project/clusters/index.html#adding-an-existing-kubernetes-cluster

## Eurodns
+ A record on Eurodns to Google endpoint

## No Auto devops, .gitlab-ci.yaml
https://docs.gitlab.com/ce/user/project/clusters/index.html#adding-an-existing-kubernetes-cluster
Looking at the Autodevops template ensure namespace does a TODO


## Resources
Deprecated: https://docs.gitlab.com/ee/user/project/integrations/kubernetes.html therefore this is deprecated too https://docs.gitlab.com/ee/ci/autodeploy/quick_start_guide.html
