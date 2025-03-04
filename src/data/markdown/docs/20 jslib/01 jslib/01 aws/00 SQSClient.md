---
title: 'SQSClient'
head_title: 'SQSClient'
description: 'SQSClient enables interaction with the AWS Simple Queue Service (SQS)'
excerpt: 'SQSClient allows interacting with the AWS Simple Queue Service (SQS)'
---

`SQSClient` interacts with the AWS Simple Queue Service (SQS). With it, the user can send messages to specified queues and list available queues in the current region. `SQSClient` operations are blocking. k6 recommends reserving their use for the [`setup`](/using-k6/test-lifecycle/) and [`teardown`](/using-k6/test-lifecycle/) stages as much as possible.

Both the dedicated `sqs.js` jslib bundle and the all-encompassing `aws.js` bundle include the `SQSClient`.

### Methods

| Function                                                         | Description                                          |
| :--------------------------------------------------------------- | :--------------------------------------------------- |
| [`sendMessage`](/javascript-api/jslib/aws/sqsclient/sqsclient-sendmessage) | Delivers a message to the specified queue.           |
| [`listQueues`](/javascript-api/jslib/aws/sqsclient/sqsclient-listqueues)   | Returns a list of your queues in the current region. |

### Throws

`SQSClient` methods throw errors in case of failure.

| Error                   | Condition                                                 |
| :---------------------- | :-------------------------------------------------------- |
| `InvalidSignatureError` | when using invalid credentials                            |
| `SQSServiceError`       | when AWS replied to the requested operation with an error |

### Example

<CodeGroup labels={[]}>

```javascript
import exec from 'k6/execution'

import { AWSConfig, SQSClient } from 'https://jslib.k6.io/aws/0.7.2/sqs.js'

const awsConfig = new AWSConfig({
    region: __ENV.AWS_REGION,
    accessKeyId: __ENV.AWS_ACCESS_KEY_ID,
    secretAccessKey: __ENV.AWS_SECRET_ACCESS_KEY,
    sessionToken: __ENV.AWS_SESSION_TOKEN,
})

const sqs = new SQSClient(awsConfig)
const testQueue = 'https://sqs.us-east-1.amazonaws.com/000000000/test-queue'

export default function () {
    // If our test queue does not exist, abort the execution.
    const queuesResponse = sqs.listQueues()
    if (queuesResponse.queueUrls.filter((q) => q === testQueue).length == 0) {
        exec.test.abort()
    }

    // Send message to test queue
    sqs.sendMessage(testQueue, JSON.stringify({value: '123'}));
}
```

</CodeGroup>