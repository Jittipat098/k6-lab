
# K6 lab results

## Part 0

- Step 0.1, http_req_duration: 336.43 ms



- Step 0.2:
  - Virtual Users: 5
  - Duration: 20 seconds
  - Total Requests: 73
  - Request Rate: 3.60 requests/s
  - Average Response Time: 334.17 ms
  - p(95): 379.57 ms
  - Max Response Time: 2.71 s
  - Failed Requests: 0%
  - Checks Succeeded: 100% (73/73)
  - Reason: The request rate was below 5 requests per second because each virtual user waits for the server response and sleeps for 1 second before starting the next iteration.


- Step 0.3:
  - Virtual Users: 5
  - Total Iterations: 20
  - Total Requests: 20
  - Request Rate: 2.85 requests/s
  - Average Response Time: 386.95 ms
  - p(95): 1.04 s
  - Max Response Time: 1.04 s
  - Failed Requests: 0%
  - Checks Succeeded: 100% (20/20)

  
- Step 0.4:
  - HTTP Status: 200
  - Total Requests: 1
  - Response Time: 289.51 ms
  - Checks Succeeded: 100% (3/3)
  - Checks Failed: 0
  - Result: The API successfully returned a pizza with a name and all ingredients were vegetarian.


- Step 0.5:
  - Expected HTTP Status: 401 Unauthorized
  - Total Requests: 1
  - Response Time: 264.01 ms
  - Checks Succeeded: 100% (1/1)
  - Checks Failed: 0
  - Failed Requests: 0%
  - Result: The API correctly rejected the request without an authorization token.

### Step 0.6: Thresholds

- Normal run (`k6 run 06-thresholds.js`):
  - P95 threshold: 1500 ms
  - Actual p(95): 378.78 ms
  - Checks succeeded: 100% (132/132)
  - HTTP request failed: 0.00%
  - Threshold result: All passed
  - Exit code: 0

- Strict run (`k6 run -e P95=300 06-thresholds.js`):
  - P95 threshold: 300 ms
  - Actual p(95): 669.21 ms
  - Checks succeeded: 100% (130/130)
  - HTTP request failed: 0.00%
  - Threshold result: http_req_duration failed
  - Exit code: 99

- Explanation: The normal run passed all thresholds. In the strict run, the p(95) response time exceeded the 300 ms limit, so the http_req_duration threshold failed and K6 returned exit code 99.


### Step 0.7: From a requirement to thresholds

- Normal run:
  - Virtual users: 3
  - Duration: 30 seconds
  - P95 threshold: less than 1000 ms
  - Actual p(95): 384.13 ms
  - P99 threshold: less than 1500 ms
  - Actual p(99): 716.83 ms
  - HTTP request failed: 0.00%
  - Checks succeeded: 100%
  - Result: All thresholds passed.

- Modified requirement run:
  - P95 threshold changed from 1000 ms to 300 ms.
  - Actual p(95): 398.63 ms
  - Actual p(99): 737.30 ms
  - HTTP request failed: 0.00%
  - Checks succeeded: 100%
  - Result: The p(95) threshold failed because 398.63 ms exceeded 300 ms.
  - Exit code: 99

- Explanation: The original requirement passed. After changing the P95 limit to 300 ms, K6 detected that the response time exceeded the threshold and returned exit code 99.
