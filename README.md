## Preconditions
Install Playwright https://playwright.dev/docs/intro

Elastic deployment/project has [required datasets](https://github.com/elastic/oblt-playwright/blob/main/docs/data_mapping.md).
Tests should be run during ongoing data collection.

## Setup 
Create .env file in the root directory with the following environmental variables:

```
ELASTIC_URL = 
ELASTIC_USERNAME = 
ELASTIC_PASSWORD = 
DATE_PICKER = 'Last 24 hours'
API_KEY = 
REPORT_FILE = ../test-results/results.json
```

Commonly used date picker options:

- Last 15 minutes
- Last 30 minutes
- Last 1 hour
- Last 24 hours
- Last 7 days
- Last 30 days

### Create alerting rules
It is recommended to create alerting rules before generating any data.
Specify relevant project name in the test command:

```
npx playwright test --project serverless_setup_alerting_rules
```

## Running tests

By default, tests run in headless mode. To launch browsers in headed mode, use the `--headed` flag.

### Run all tests
Specify project name (`stateful` or `serverless`) in the test command. Example:

```
npx playwright test --project serverless --headed
```

### Run a specific test
Specify test name in the test command. Example:

```
npx playwright test apm.serverless.spec.ts --project serverless --headed
```

### Run an authorization test
Execute `serverless.auth.ts` or `stateful.setup.ts` as follows:

```
npx playwright test serverless.auth.ts
```

### API testing
To authorize access to Elasticsearch resources, pass your API key into `API_KEY` environmental variable. To run a suite of API tests, specify correspondent project name (`api`) in the following command:

```
npx playwright test --project api
```

## Get Elasticsearch-friendly JSON test report 

Playwright spits out JSON test reports that have nested structure, which not quite suitable for Elasticsearch - results for each test is a separate array with its own fields. The problem is nested field type is not supported in Kibana visualizations. To solve this, use [this script](https://github.com/elastic/oblt-playwright/blob/main/tools/split_report.ts) to flatten and split a report by each test:

```
node tools\split_report.ts
```
<details>
<summary>Here is an example of how the outcome of that script might look like</summary>

```
{
  "title": "Infrastructure - Cluster Overview dashboard",
  "startTime": "2024-02-02T12:50:18.767Z",
  "status": "passed",
  "duration": 59414,
  "step01": 4351,
  "step02": 1064,
  "step03": 24160,
  "workerIndex": 1,
  "retry": 0,
  "errors": [],
  "timeout": 300000
}
```
</details>

Resulting files stored in the same directory as the original report.