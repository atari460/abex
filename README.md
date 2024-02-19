# abEx

abEx is an API that enables to conduct A/B experiments (see https://hbr.org/2017/06/a-refresher-on-ab-testing for information on what an A/B experiment is). Users can create experiments, randomly allocate users to either side, force users to either side of the experiment, adjust traffic rates, get information about the experiment and current allocations, and end experiments based on either sample size, duration in days, or by force.

## Basic Use
1. [Create](#create-experiment) your experiment
2. [Start](#start-experiment) your experiment
3. Allocate users
4. Stop or restart your experiment
5. (if needed) Update your experiment decision
At any point after creation, you can also get the status of your experiment and/or rename your experiment

## Create Experiment
### Request
`POST /create`
| Param | Type | Required | Accepted Values | Description |
| :--- | :--- | :--- | :--- | :--- |
| `experimentName` | string | Required | String 3-75 characters long containing any letters, numbers, hypens, periods, and/or underscores  | Name of your experiment |
```json
{
    "experimentName" : "MyFirstExperiment"
}
```

### Response
```json
{
    "status": "success",
    "experimentInfo": {
        "experimentName": "MyFirstExperiment",
        "experimentId": 17945202,
        "createdAt": "2024-02-19T10:17:40.994Z",
        "experimentStatus": "Not started"
    }
}
```
**IMPORTANT:** make sure you save the value returned in the `experimentId` field. This is a required parameter, in combination with your experiment name, for all other endpoints and this is the only time the API will provide this ID to you. **If you lose this you will be unable to further interact with your experiment.**

## Start Experiment
### Request
`POST /start`
| Param | Type | Required | Accepted Values | Description |
| :--- | :--- | :--- | :--- | :--- |
| `experimentName` | string | Required | Any existing experiment | Name of the experiment you wish to start |
| `experimentId` | int | Required | Any existing experiment | ID of your experiment, provided during creation (/create) |
| `sampleSize` | int | Optional | Up to 1,000,000 (minimum value is 2) | Total number of allocations to either the A or B side of your experiment you wish to capture before the experiment stops. Experiment will stop automatically once this number has been reached (sum of allocations to A + B). |
| `durationDays` | int | Optional | Up to 90 (minimum value is 1) | Duration in days you wish to run the experiment. Experiment will automatically stop exactly that many days after starting (i.e. if you start at 11:59pm on January 1 and run for 2 days, it will stop at 11:59pm on January 3) |
| `trafficRate` | int | Optional | 1-100 | Defaults to 100. Defines the % of users who are allocated to either A or B / unallocated users. For example, if you set the traffic rate to 50, this means that 50% of the total users will be allocated to either A or B, while the remaining 50% will be unallocated (U). |

If both sampleSize and durationDays are not provided, the experiment will default to run for 14 days with no limit to sample size. If only durationDays is provided, there will be no limit to sampleSize and visa versa.
```json
{
    "experimentName":"MyFirstExperiment",
    "experimentId":17945202,
    "durationDays":30,
    "trafficRate":100
}
```

### Response
```json
{
    "status": "success",
    "experimentInfo": {
        "experimentName": "MyFirstExperiment",
        "experimentId": 17945202,
        "createdAt": "2024-02-19T10:17:40.994Z",
        "experimentStatus": "Running",
        "runData": {
            "startDate": "2024-02-19T10:42:33.066Z",
            "durationDays": 30,
            "endDate": "2024-03-20T10:42:33.066Z",
            "trafficRate": 100
        }
    }
}
```
