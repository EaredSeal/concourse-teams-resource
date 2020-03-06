# concourse-teams-resource
## Microsoft Teams
Create a webhook endpoint for a microsoft teams channel

## Param Configuration

* `status`: *Required.* allowed values: success, failure
* `message`: *Optional.*
* `activitySubtitle`: *Optional.*

Resolves at runtime Concourse CI environment variables referenced in your Teams
connector messages such as:

```
$BUILD_ID
$BUILD_NAME
$BUILD_JOB_NAME
$BUILD_PIPELINE_NAME
$BUILD_TEAM_NAME
$ATC_EXTERNAL_URL
```

## Pipeline
```yaml
resource_types:
- name: teams-notification
  type: docker-image
  source:
    repository: dhpizza/concourse-to-teams-hook-image
    tag: latest

resources:
- name: alert
  type: teams-notification
  source:
    url: <microsoft teams webhook url for channel>

jobs:
- name: teams-notification
  serial: true
  on_failure:
    do:
    - put: alert
      params:
        status: failure                   # required, allowed values: success, failure
        additionalLinkFile: src/url.txt   # optional, name of the file that contains an additional link to display next to the concourse link
        additionalLinkName: "Click me!"   # optional, display text for additional link
  plan:
  - task: fail
    config:
      platform: linux
      image_resource:
        type: docker-image
        source:
          repository: alpine
      run:
        path: sh
        args:
        - -exc
        - |
          exit 1
```
