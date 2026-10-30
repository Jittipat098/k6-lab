
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
