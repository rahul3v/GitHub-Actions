# GitHub Actions
GitHub Actions Tests, to help learn github action easily, Just the basics to get you started with some clearity

# The File structure details

```
|-- github-actions (repository)
|   |__ .github
|       └── workflows                   # contains number of workflow actions to run
|           └── my-workflow.yml
|       └── actions                     # contains number of actions used in you workflows
|           └── my-action.yml
|       └── scripts                     # contains number of scripts used in you workflows
|           └── my-script.sh
|       └── FUNDING.yml                 # if enabled github sponsor
```
<br><br>
### WORKFLOWS
Workflows to run `.github/workflows/main.yml`
```yaml
name:GitHub-Action-Name                 # GitHub-Action-Name

on : [puch|pull_request|issues]         # When to run, for more use with comma as `[pull_request, issues]`

#OR

on :                                    # when and which branch to run
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  issues:
    branches: [ main ]

#OR

on :                                    # Schedule at a time to run
   schedule:                            # schedule to run on UTC timezone
    - cron: "30 1 * * *"                # format: min[0:59] hr[0:23] day[1:31] month[0-11] weekday[0-6]; week-0:sun, week-6:sat
    
  workflow_dispatch:                    # Allows you to run this workflow manually from the Actions tab

jobs:
  example-job:                          # Job tag-name
    runs-on: ubuntu-latest              # platform to run on, options available [ubuntu-latest|windows-latest|macos-latest]
    steps:                              # Steps to follow
      - name: Checkout                  # Job Steps-name
        uses: actions/checkout@v2       # ACTION to use, defaults can be used from github's 'actions/*' repository as per need
  
      - name: Connect to PostgreSQL     # Job Steps-name
        run: node client.js             # SCRIPT to run, direct or via bash scripts
        env:                            # .env variables to use if any
          POSTGRES_HOST: postgres       # .env variable names
          POSTGRES_PORT: 5432
          mysecret: ${{secrets.MySECRET}}  # Any secret made on repository secrets, can access here via variable `secrets.variableName`
  
      - name: Run build script
        run: ./.github/scripts/hello.sh # location of bash script, to run
        shell: bash                     # used if running from any bash scripts, for other types, use `[bash|pwsh|python|sh|cmd|powershell]` as per need

  example-job2:
    runs-on: ubuntu-latest
    name: A job to say hello
    steps:
      - name: Checkout                  # Job Steps-name
        uses: actions/checkout@v2       # To use the repository's private custom action, you must checkout the repository
      
      - name: Hello world action step
        id: hello-variable              # to access the actions `outputs:` block result, and store in it, here in 'hello-variable'
        uses: ./.github/actions/action.yml    # custom actions
        with:                           # to pass any input variables insde your used action file mentioned under `uses:` block
          input-variable-name: 'data value' 
          input-variable-name2: 'data value2'
        
      - name: Get the output result     # Use the output from the `hello` step
        run: echo "The output was ${{ steps.hello-variable.outputs.out-variable-name }}"
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
**WORKFLOWS** runs as per sheduled mentioned (under `on:` block), and uses **actions** (under `uses:` block if any) and **scripts** (under `run:` block if any) as needed

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
