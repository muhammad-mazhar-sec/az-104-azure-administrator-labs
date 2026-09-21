# Lab 09a: Cost-Optimized Azure Web Apps

## Overview

This lab implemented a PHP web application on Azure App Service, created a staging deployment slot, configured external Git deployment, swapped the staging slot into production, configured scale-out settings, and created an Azure Load Testing resource.

The lab used a cost-optimized configuration. The original AZ-104 exercise specifies a Premium V3 App Service Plan; this implementation used a Standard S1 Linux App Service Plan because it supports the required deployment-slot, slot-swap, and scaling concepts at a lower hourly cost.

The resource group was deleted after evidence capture to avoid ongoing charges.

## Architecture

```text
                            Internet
                               |
                               v
             Production Web App: az104-webapp
             PHP 8.2 on Linux / Standard S1 Plan
                               |
                     Production deployment slot
                               |
                  Swap validated staging content
                               |
                               v
                Staging slot: az104-webapp/staging
                External Git deployment source
                Azure-Samples/php-docs-hello-world
                               |
                               v
                     Hello World PHP application

                  App Service Plan: asp-lab09-linux
                  Linux / Standard S1
                  Supports deployment slots and scaling

                  Azure Load Testing: testingloadonweb
                  Resource created; test execution not completed
```

## Resource Inventory

| Resource | Name | Configuration |
|---|---|---|
| Resource group | `az104-rg9` | Temporary lab resource group; deleted after lab |
| Production web app | `az104-webapp` | PHP 8.2, Linux |
| Staging slot | `az104-webapp/staging` | Created without cloning production settings |
| App Service Plan | `asp-lab09-linux` | Standard S1 Linux |
| Deployment source | `Azure-Samples/php-docs-hello-world` | External Git repository |
| Load Testing resource | `testingloadonweb` | Azure Load Testing resource |

## Cost Optimization

```text
Original plan: Premium V3 P1V3
Selected plan: Standard S1 Linux
Minimum instances: 1
Maximum instances: 2
```

Standard S1 was sufficient for the tasks completed in this lab:

- Staging deployment slot.
- External Git deployment.
- Deployment-slot swap.
- Scale-out settings.

The lab resources were deleted after screenshots were captured.

## Web App Deployment

The production web app was created with:

```text
Publish: Code
Runtime stack: PHP 8.2
Operating system: Linux
Region: East Asia
App Service Plan: asp-lab09-linux
Pricing tier: Standard S1
```

## Deployment Slot Workflow

A staging deployment slot was created with these settings:

```text
Slot name: staging
Clone settings from: Do not clone settings
```

The staging slot was configured to deploy from the external Git repository:

```text
Repository: https://github.com/Azure-Samples/php-docs-hello-world
Branch: master
```

After validating the staging deployment, the staging slot was swapped with the production slot. This demonstrates a controlled release workflow: validate a release in staging before promoting it to production.

## Scale-Out Configuration

The App Service Plan was configured with cost-safe scaling limits:

```text
Minimum instances: 1
Maximum instances: 2
```

This demonstrates App Service scale-out configuration while preventing unplanned growth beyond two instances.

## Load Testing Note

An Azure Load Testing resource named `testingloadonweb` was created and a minimal URL-based test configuration was prepared:

```text
Engine instances: 1
Load pattern: Linear
Concurrent users per engine: 1
Test duration: 1 minute
Ramp-up time: 1 minute
```

The Azure portal remained in a loading state while resolving the Load Testing resource during test creation. The test was **not created a second time and was not executed**. This was intentional to avoid duplicate resources and unnecessary charges.

The captured screenshot records the validated test-plan configuration only. It does not represent live test metrics or a completed test run.

## Evidence

The documentation file belongs in:

```text
07-app-service/lab-09a-implement-web-apps.md
```

The screenshot files belong in:

```text
07-app-service/lab-09a-screenshots/
```

### Load Test Configuration

The file is named `08-load-test-live-metrics.jpg`, but it is actually the **load-test configuration review**, not live metrics. The screenshot is embedded below with its exact existing filename.

![Azure Load Testing configuration review](lab-09a-screenshots/08-load-test-live-metrics.jpg)

### Resource Topology

Azure Resource Visualizer shows the relationship between the production App Service, staging slot, App Service Plan, and Azure Load Testing resource.

![Azure Web App, staging slot, App Service Plan, and Load Testing topology](lab-09a-screenshots/09-web-app-resource-topology.jpg)

## Why Images Render

This Markdown document is stored in the `07-app-service` directory, alongside the `lab-09a-screenshots` folder. Therefore, the correct relative image path is:

```markdown
![Description](lab-09a-screenshots/file-name.jpg)
```

The path intentionally does **not** begin with `./07-app-service/`, because that would incorrectly repeat the parent directory when GitHub renders the file.

## Screenshot Directory

```text
07-app-service/
├── lab-09a-implement-web-apps.md
└── lab-09a-screenshots/
    ├── 08-load-test-live-metrics.jpg
    └── 09-web-app-resource-topology.jpg
```

## Learning Outcomes

This lab demonstrated:

- Azure App Service as a PaaS platform for web applications.
- PHP web hosting on Linux.
- App Service Plan cost and feature trade-offs.
- Deployment-slot creation.
- External Git deployment to a staging slot.
- Deployment-slot swapping from staging to production.
- Cost-safe App Service scale-out configuration.
- Azure Load Testing resource and minimal test configuration.
- Azure Resource Visualizer topology review.

## Cleanup

All temporary Lab 09a resources were deleted after evidence capture.

```bash
az group delete --name az104-rg9 --yes --no-wait
```

Deleting the resource group removes the App Service, staging slot, App Service Plan, Azure Load Testing resource, and related lab resources.

## Security Notes

- No passwords, publishing credentials, connection strings, or access tokens are included in this document.
- The test was not executed; no test results are claimed.
- The resource group was deleted after the lab to control costs.
