# concourse-teams-resource
## Microsoft Teams
Create a webhook endpoint for a microsoft teams channel

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

