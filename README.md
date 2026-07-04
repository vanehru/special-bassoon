# special-bassoon

Got it. The sync workflow stays generic. For each project, you customize the CI workflow based on its technology stack.

## Flow overview

When a developer pushes changes to the develop branch, the sync workflow is triggered automatically. It updates the feature/develop branch to match the latest state of develop, pushes the updated branch to GitHub, and then dispatches the CI workflow for feature/develop. This ensures that the feature branch remains aligned with develop and that its latest changes are validated through the appropriate build and test pipeline.

## CI workflow setup by project type:

### Node.js project
```yaml
"on":
  push:
    branches:
      - feature/develop
  workflow_dispatch:

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5
      - uses: actions/setup-node@v4
        with:
          node-version: '24'
      - run: npm ci
      - run: npm run build
      - run: npm test
```

### Python project
```yaml
"on":
  push:
    branches:
      - feature/develop
  workflow_dispatch:

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5
      - uses: actions/setup-python@v4
        with:
          python-version: '3.11'
      - run: pip install -r requirements.txt
      - run: pytest
```

### Terraform/Infrastructure project
```yaml
"on":
  push:
    branches:
      - feature/develop
  workflow_dispatch:

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5
      - uses: hashicorp/setup-terraform@v2
      - run: terraform fmt -check src/infra/
      - run: terraform validate src/infra/
```

### .NET project
```yaml
"on":
  push:
    branches:
      - feature/develop
  workflow_dispatch:

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5
      - uses: actions/setup-dotnet@v3
        with:
          dotnet-version: '8.0'
      - run: dotnet build
      - run: dotnet test
```

## Pattern
For your repo with Terraform in infra, use the **Terraform setup** above. Just keep the `workflow_dispatch:` trigger so the sync workflow can dispatch it.
