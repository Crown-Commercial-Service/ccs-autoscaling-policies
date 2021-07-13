# ccs-autoscaling-policies

This repository contains a list of example JSON policies that can be used as a template to bind to your application on
gov.uk PaaS

### Metrics

You can scale your app based on some provided metrics:

- memoryused - how many megabytes of memory an app instance is using
- memoryutil - percentage of memory utilization where 50 is 50%
- cpu - percentage of CPU utilization where 100 is 100% of a single CPU
- responsetime - average time in milliseconds an app instance takes to respond to external HTTP requests
- throughput - number of external HTTP requests processed per second

### Examples

Example policies for each of these metrics can be found in the [example_policies](example_policies) directory. It is
also possible to combine multiple metrics within the same ASG policy (see the [multi_metric_policy](example_policies/multi_metric_policy.json)
json file)

#### Schedules

You can also scale your application on a given schedule (for example, if you have an application that is frequently used
during daytime working hours, but does not receive much traffic otherwise) - see the [schedule.json](example_policies/schedule.json)
file for an example of this. This example policy will ensure:

- There are between 6 and 10 instances during Wednesday working hours
- There are between 4 and 8 instances during working hours on other weekdays
- There are between 2 and 4 instances otherwise

It is also possible to scale your application on a monthly basis (e.g. you have an app frequently used in the first week
of the month, but does not receive much traffic otherwise) - see [monthly_schedule.json](example_policies/monthly_schedule.json)
for an example of this. This policy will ensure:

- There are between 6 and 10 instances during daylight hours of the first week of the month
- There are between 2 and 4 instances otherwise

### How to bind an autoscaling policy to an application in GPaaS

Ensure the autoscaling CLI plugin is installed:

`cf install-plugin -r CF-Community app-autoscaler-plugin`

Deploy your app, where APPNAME is the name of your application:

`cf push APPNAME`

Create an autoscaler service to scale your app:

`cf create-service autoscaler autoscaler-free-plan scale-APPNAME`

Note: If your organisation/space is using Terraform/IAC, this should be provided to you by default. Please speak to TechOps
if you require assistance with this

Bind the autoscaler service to your app:

`cf bind-service APPNAME scale-APPNAME`

Create the desired autoscaling policy (see [example_policies](example_policies))

Attach the autoscaling policy to your app:

`cf attach-autoscaling-policy APPNAME policy.json`

Observe the app scaling automatically:

`cf autoscaling-history APPNAME`
