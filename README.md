# GitHub Actions
GitHub Actions Tests, to help learn action github easily

# The File structure details

```
|-- github-actions (repository)
|   |__ .github
|       └── workflows               # contains number of workflow actions to run
|           └── my-workflow.yml
|       └── actions                 # contains number of actions used in you workflows
|           └── my-action.yml
|       └── scripts                 # contains number of scripts used in you workflows
|           └── my-script.sh
|       └── FUNDING.yml
```
<br><br>
### WORKFLOWS
Workflows to run `.github/workflows/main.yml`
```yaml
name:GitHub-Action-Name             # GitHub-Action-Name

on : [puch|pull_request|issues]     # When to run

#OR

on :                                # when and which branch to run
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  issues:
    branches: [ main ]

#OR

on :                                # Schedule at a time to run
   schedule:                        # schedule to run on UTC timezone
    - cron: "30 1 * * *"            # format: min[0:59] hr[0:23] day[1:31] month[0-11] weekday[0-6]; week-0:sun, week-6:sat
    
  workflow_dispatch:                # Allows you to run this workflow manually from the Actions tab

jobs:
  example-job:                      # Job tag-name
  runs-on: ubuntu-latest            # platform to run on
  steps:                            # Steps to follow
  - uses: actions/checkout@v2       # ACTION to use, defaults can be used from github's 'actions/*' repository as per need
  
  - name: Connect to PostgreSQL     # Job Steps-name
    run: node client.js             # SCRIPT to run, direct or via bash scripts
    env:                            # .env variables to use if any
       POSTGRES_HOST: postgres      # .env variable names
       POSTGRES_PORT: 5432
       mysecret: ${{secrets.MySECRET}}  # Any secret made on repository secrets, can access here via variable `secrets.variableName`
  
  - name: Run build script
    run: ./.github/scripts/hello.sh # location of bash script, to run
    shell: bash                     # used if running from any bash scripts
```

##### Sequential jobs
```yaml
# Sequential jobs
jobs:
  setup:
    runs-on: ubuntu-latest
    steps:
      - run: ./setup_server.sh
  build:
    needs: setup                  # must pass setup, to run further
    runs-on: ubuntu-latest
    steps:
      - run: ./build_server.sh
  test:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - run: ./test_server.sh

```
#### Remember !
**WORKFLOWS** runs as per sheduled mentioned (under `on:` block), and uses **actions** (under `use:` block if any) and **scripts** (under `run:` block if any) as needed

<br><br>

### ACTIONS
Actions to run on any workflows `.github/actions/action.yml`
```yaml
name: 'My Action Name'                # Action Name
description: 'My action Description'  # Overview of action, what it will do
inputs:
  input-variable-name:                # id of input variable
    description: 'Who to greet'       # small overview
    required: true                    # required variable or not
    default: 'World'
outputs:
  output-variable-name:               # id of output variable
    description: 'my output result'   # small overview
runs:
  using: 'node12'                     # version of platform
  main: 'index.js'                    # file to pass your input variables, as well as return at output variable

```

<br>

Install this two before writing your `index.js` file

```bash
npm install @actions/core
npm install @actions/github
```

**index.js**
```js
const core = require('@actions/core');
const github = require('@actions/github');

try {
  const input = core.getInput('input-variable-name');   // get input defined in actions filename 'action.yml'
  console.log(`my input : ${input}`);
  
  const output = "You have learnt somthing today, good luck !";
  core.setOutput("output-variable-name", output);       // set output defined in actions filename 'action.yml'
  
  // Get the JSON webhook payload for the event that triggered the workflow
  const payload = JSON.stringify(github.context.payload, undefined, 2)
  console.log(`The event payload: ${payload}`);

} catch (error) {
  core.setFailed(error.message);
}
```

<br><br>
### SCRIPTS
contains bash files as per the requirement `.github/scripts/script.sh`
```bash
# hello.sh
echo "hello world, this is my first github action script"
```

<br><br>
### Github Sponsor
If you enabled github sponsor then add `.github/FUNDING.yml` with your details
```yaml
github: [USERNAME1,USERNAME2]       # to add github users as sponser
patreon: USERNAME                   # to add patreon username
open_collective: USERNAME           # to add open collective username
ko_fi: USERNAME                     # to add buy me coffee username
issuehunt: USERNAME                 # to add issuehunt username
custom: [LINK1,LINK2,....LINK-N]    # to add any custom link
```
