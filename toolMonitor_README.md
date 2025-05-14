  
# Tool Monitor

This application is used to keep track of each each instance hosted on AWS EC2 Servers.

## Installation

The application can be accessed [here](https://toolmonitor.hlamtools.trade/).

## User Guide

### RDP

Briefly outlines all information of the Instance that is hosted on the AWS EC2 servers such as:

* Instance ID - The AWS instance ID.
* Key Pair - What key is required to access the EC2 instance.
* Notes - Brief detail on what tools the instance hosts.
* Actions - Downloads the rdp file for the instance.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/tool-monitor-rdp.png)

### Monitoring

Briefly outlines all tasks that are currently stored in each Instance that is hosted on the AWS EC2 servers.

* Instance Name - The AWS instance that is beinf referred to.
* Task Name - The task name that is stored.
* Status - Is depicted in either of the two states listed below:
  * Ready - Task is not currently running but can be readily run.
  * Running - Task is currently running.
* Last Ping - Indicates the time of when the status of the task was last checked.
* Actions - Manually starts the task that is stored in the instance.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/tool-monitor-monitoring.png)