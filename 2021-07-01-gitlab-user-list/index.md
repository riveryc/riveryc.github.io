# Gitlab user list


It's hard to believe that Gitlab doesn't have such feature to export an user list to CVS file...

<!--more-->

# How to... 

TL;DR:

Gitlab User API: https://docs.gitlab.com/ee/api/users.html

# Code:

```python
import requests, json, csv

baseUrl = "https://<your gitlab url>/api/v4"
privateToken = "your private Token"

headers = {
    'PRIVATE-TOKEN': privateToken
}

# List users
def getAllUsers():
    allUsers = []
    userResp = requests.get(baseUrl + "/users?active=true&per_page=100", headers = headers)
    maxPages = int(userResp.headers['X-Total-Pages'])
    for i in range(maxPages):
        pNum = i + 1
        usersOnPage = requests.get(baseUrl + "/users?active=true&per_page=100&page=" + str(pNum), headers = headers)
        for u in usersOnPage.json():
            allUsers.append(u)

    return allUsers

allUsers = getAllUsers()
allUsersCreatedAt = sorted(allUsers, key=lambda x: x['created_at'])

i = 1
csvData = []
for x in allUsersCreatedAt:
    eventResp = requests.get(baseUrl + "/users/" + str(x['id']) + "/events", headers = headers)
    userEvents = eventResp.json()
    actionName = None
    eventCreatedAt = None
    eventType = None
    externUid = None
    if len(userEvents) > 0 and x['state'] == 'active':
        actionName = userEvents[0]['action_name']
        eventType = userEvents[0]['target_type']
        eventCreatedAt = userEvents[0]['created_at']
        # Enable following if you are using LDAP, will show your external id
        # externUid = x['identities'][0]['extern_uid'][x['identities'][0]['extern_uid'].index("ou"):]
    # print(i, x['id'], x['name'], externUid, x['created_at'], x['last_activity_on'], actionName, eventType, eventCreatedAt)
    print(i, x['id'], x['name'], x['created_at'], x['last_activity_on'], actionName, eventType, eventCreatedAt, x['using_license_seat'])
    # csvData.append([i, x['id'], x['name'], externUid, x['created_at'], x['last_activity_on'], actionName, eventType, eventCreatedAt])
    csvData.append([i, x['id'], x['name'], x['created_at'], x['last_activity_on'], actionName, eventType, eventCreatedAt, x['using_license_seat']])
    i = i + 1

with open('gitlab-users.csv', 'w') as csvFile:
    writer = csv.writer(csvFile)
    writer.writerows(csvData)
csvFile.close()
```
