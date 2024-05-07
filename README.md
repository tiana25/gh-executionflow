## Simple React App To Practice GitHub Workflows Techniques & Strategies

**There are 5 workflows:**
- **Website deployment**
  
  This workflow is created to practice *"Controlling workflow & Job Execution"*.
  The default behaviour of GitHub actions is if one step fails - the entire job to which this step belongs is cancelled.
  Other (dependent) jobs (using `needs`) would be cancelled / aborted if a previous job fails.
  There are 4 special conditions that can be used in "if" conditions: `failure()`, `success()`, `always()`, `cancelled()`.
  Here I'm using `failure()` condition to upload test report.

  Also I'm using an optimizing cache technique.
  ```
  - name: Cache dependencies
    id: cache
    uses: actions/cache@v3
    with:
     path: node_modules
     key: deps-node-modules-${{ hashFiles('**/package-lock.json') }}
  - name: Install dependencies
    if: steps.cache.outputs.cache-hit != 'true'
    run: npm ci
  ```
- **Continue Workflow**

  This workflow is created to practice another useful concept as `continue-on-error`.
  If we set `continue-on-error` to true, we’re saying that the job should continue execution even if that step fails. But it’s not the same as with `if` in the above workflow.
  Because with `if`, the step failed, therefore the whole job failed.
  With `continue-on-error` the workflow succeeded despite test code producing an error.

  If we want our entire workflow to continue even if that step fails, then we should use `continue-on-error`.
  If on the other hand we want to run some selected steps or jobs, not the overall workflow - we use `if` checks.

- **Matrix Demo**

  This workflow is used to practice *"Understanding & using matrix strategies"* & *"Including & Excluding values (Matrix strategy)"*.
  
  **Example:**
  What matrix will do here is it will tell GitHub Actions to run the build job multiple times once per value in that operating system array. Those jobs will be run in parallel by default.
  Also we can include single combinations or exclude certain combinations from the generated metrics.
  ```
    jobs:
    build:
      continue-on-error: true
      strategy:
        matrix:
          node-version: [12, 14, 16]
          operating-system: [ubuntu-latest, windows-latest]
          include:
            - node-version: 18
              operating-system: ubuntu-latest
          exclude:
            - node-version: 12
              operating-system: windows-latest
  ```
- **Reusable Deploy**
  
  This workflow was created to practice *"Saving time & code with reusable workflows"*.
  This workflow will be kicked of by Github Actions when another workflow which is calling it starts.
  In this workflow it's shown how it's possible to accept inputs in reusable workflows & define outputs.
  
  **Example:**
  ```
  name: Reusable Deploy
  on: workflow_call
  jobs:
    deploy:
      runs-on: ubuntu-latest
      steps:
        - name: Output information
          run: echo "Deploying & uploading..."
  ```
- **Using Reusable Workflow**
  
  This workflow calls the *"Reusable Deploy"* workflow defined above.
  In this workflow defined examples of how we can add inputs to the reusable workflow, secrets and receive outputs.
