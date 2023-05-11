# Logging Time

One aspect of achieving real-time RD&I management is tracking the time your team invests in RD&I activities. ReaDI-Watch offers a full time tracking feature, which can also be accessed via our API. In this guide you'll see how to create a timelog for a member of your team, and retrieve a list of previous timelogs for that team member.

You could use a similar integration pattern for use cases including:

- Automatically adding ReaDI-Watch timelogs when your employees submit timesheets in your HR system
- Adding ReaDI-Watch timelogs based on RFID swipe cards
- Integrating to your project management systems that also log time, such as Jira, Asana, or Trello.

## 1. Lookup Reference Details

Creating a timelog requires, at minimum, identifying your `Account`, the RD&I `Project`, the `Team Member`, the related `User`, and the `Work Package`.

First, obtain your `accountId` using the [`/accounts`](https://app.readi-watch.com/api-docs/reference) endpoint:

```curl
curl --location 'https://app.readi-watch.com/api/1.1/wf/accounts' \
--header 'Authorization: Bearer [YourToken]'
```

This will return a response like this. Save the `accountId` for use in the create timelog call.

```json
{
    "data": [
        {
            "accountId": "[YourAccountId]",
            "accountName": "[Your Account NAme]",
            "_link": "https://app.readi-watch.com/api/1.1/wf/accounts?accountId=[YourAccountId]]"
        }
    ],
    "pagination": {
        "offset": 0,
        "total": 1
    }
}
```

Then, use the [`/projects`](https://app.readi-watch.com/api-docs/reference) endpoint to view a list of the projects set up in your account. You could use this to display a list of projects to users inside the application or integration you are building.

```curl
curl --location 'https://app.readi-watch.com/api/1.1/wf/projects' \
--header 'Authorization: Bearer [YourToken]'
```

The list of currently set up projects and their important content will be returned, and contains important Id values that will be needed in the next step.

>> The ability to create projects and other data via the ReaDI-Watch API is coming soon.

```json
{
    "data": [
        {
            "projectId": "[projectId]",
            "projectName": "[Your Project Title]]",
            "_link": "https://app.readi-watch.com/api/1.1/wf/projects?projectId=[projectId]]",
            "workPackages": [
                {
                    "workPackageName": "Work Package Name",
                    "workPackageId": "[workPackageId]",
                    "teamMembers": [
                        {
                            "name": "First Name Last Name",
                            "teamMemberId": "[teamMemberId]",
                            "userId": "[userId]]",
                            "teamMemberRole": "Responsible",
                            "email": "[user email]"
                        }
                    ]
                }
            ]
        }
    ],
    "pagination": {
        "offset": 0,
        "total": 1
    }
}
```

## 2. Create a Timelog

Create a timelog using the [`/create_timelog`](https://app.readi-watch.com/api-docs/reference) endpoint. You'll need the IDs retrieved in the previous step. Set the year, month, and day to integers, make sure to provide an activity description, and set the time in hours.

```curl
curl --location 'https://app.readi-watch.com/api/1.1/wf/create_timelog' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer [YourToken]' \
--data '{
  "year": 2023,
  "month": 5,
  "day": 11,
  "accountId": "[your account id]",
  "activity": "A description of the activity",
  "projectId": "[project id]",
  "teamMemberId": "[team member id]",
  "userId": "[user id]",
  "workPackageId": "[work package id]",
  "time": 4
}'
```

A successful response will include the timelogId.

```json
{
    "data": [
        {
            "timelogId": "[timelogId]]",
            "project": {
                "projectId": "[projectId]",
                "projectName": "[project name]",
                "_link": "https://app.readi-watch.com/api/1.1/wf/projects?projectId=[projectId]"
            },
            "workPackage": {
                "workPackageId": "[workPackageId]",
                "workPackageName": "[work package name]"
            },
            "investigation": {
                "investigationId": "[investigationId]",
                "investigationName": "[investigation nmame]"
            },
            "user": {
                "name": "[user first and last name]",
                "userId": "[userId]",
                "email": "[user email]"
            },
            "time": 4,
            "date": "[yyyy-mm-dd]",
            "activity": "A description of the activity",
            "createdAt": "May 11, 2023 11:45 am"
        }
    ]
}
```

## 3. Retrieve Timelogs

If you later need to obtain the timelog created in the previous step, or a list of timelogs based on some particular criteria, use the [`/timelogs`](https://app.readi-watch.com/api-docs/reference) endpoint.

For instance, you can obtain all timelogs inside of a date range:

```curl
curl --location 'https://app.readi-watch.com/api/1.1/wf/timelogs?startDate=2023-05-01&endDate=2023-05-11' \
--header 'Authorization: Bearer [YourToken]'
```

You can also search by the email of the timelog user:

```curl
curl --location 'https://app.readi-watch.com/api/1.1/wf/timelogs?email=[userEmail]' \
--header 'Authorization: Bearer [YourToken]'
```

In either case, a list of matching timelogs will be returned:

```json
{
    "data": [
        {
            "timelogId": "[timelogId]]",
            "project": {
                "projectId": "[projectId]",
                "projectName": "[project name]",
                "_link": "https://app.readi-watch.com/api/1.1/wf/projects?projectId=[projectId]"
            },
            "workPackage": {
                "workPackageId": "[workPackageId]",
                "workPackageName": "[work package name]"
            },
            "investigation": {
                "investigationId": "[investigationId]",
                "investigationName": "[investigation nmame]"
            },
            "user": {
                "name": "[user first and last name]",
                "userId": "[userId]",
                "email": "[user email]"
            },
            "time": 4,
            "date": "[yyyy-mm-dd]",
            "activity": "A description of the activity",
            "createdAt": "May 11, 2023 11:45 am"
        }
    ],
    "pagination": {
        "offset": 0,
        "total": 13
    }
}
```
