
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

### Step 0.8: Bug Hunt

- Initial test:
  - Virtual users: 5
  - Duration: 20 seconds
  - Total requests: 75
  - Checks succeeded: 85.77% (193/225)
  - Checks failed: 14.22% (32/225)
  - The `no pepperoni` check failed 7 times.
  - The `no knife needed` check failed 25 times.

- Investigation:
  - Used `explore.js` to inspect the available toppings and tools from the API.
  - The API returned the ingredient name as `Pepperoni` and the tool name as `Knife`.
  - The original request used lowercase values: `pepperoni` and `knife`.

- Fix:
  - Changed `pepperoni` to `Pepperoni`.
  - Changed `knife` to `Knife`.

- Final test:
  - Virtual users: 5
  - Duration: 20 seconds
  - Total requests: 75
  - Checks succeeded: 100% (225/225)
  - Checks failed: 0%
  - HTTP request failed: 0%
  - Result: All checks passed.

- Explanation:
  - The request used lowercase names that did not match the names returned by the API.
  - Load testing sent many requests, making it easier to detect inconsistent results that might not appear in a single manual request. After correcting the names, all checks passed.

  ## Part A: Load Testing

### A1: Smoke Test

- Virtual Users: 1
- Duration: 30 seconds
- Total Requests: 15
- Request Rate: 0.44 requests/s
- Average Response Time: 308.17 ms
- p(95): 368.09 ms
- Max Response Time: 372.02 ms
- Failed Requests: 0%
- Checks Succeeded: 100% (20/20)
- Result: All thresholds passed. The application successfully handled the basic workload with one virtual user.

### A2: Average Load Test

- Maximum Virtual Users: 3
- Duration: 3 minutes
- Total Requests: 228
- Request Rate: 1.24 requests/s
- Average Response Time: 346.08 ms
- p(95): 463.58 ms
- Max Response Time: 1.13 s
- Failed Requests: 0%
- Checks Succeeded: 100% (304/304)
- Home page p(95): 406.46 ms
- Recommendation p(95): 831.34 ms
- Result: All thresholds passed. The application handled the average workload of three virtual users successfully without failed requests.

### A3: Stress Test

- Maximum Virtual Users: 5
- Duration: 3 minutes
- Total Requests: 399
- Request Rate: 2.16 requests/s
- Average Response Time: 296.39 ms
- p(95): 374.74 ms
- Max Response Time: 741.37 ms
- Failed Requests: 0%
- Checks Succeeded: 100% (532/532)
- Home page p(95): 321.20 ms
- Recommendation p(95): 394.47 ms
- Result: All thresholds passed. The application handled five virtual users successfully without failed requests.

### A4: Spike Test

- Maximum Virtual Users: 5
- Duration: 2 minutes 20 seconds
- Total Requests: 150
- Request Rate: 1.05 requests/s
- Average Response Time: 305.13 ms
- p(95): 381.95 ms
- Max Response Time: 806.37 ms
- Failed Requests: 0%
- Checks Succeeded: 100% (200/200)
- Home page p(95): 292.13 ms
- Recommendation p(95): 457.81 ms
- Result: All thresholds passed. The application handled the sudden increase to five virtual users without failed requests.

### A5: Soak Test

- Maximum Virtual Users: 2
- Duration: 11 minutes
- Total Requests: 651
- Request Rate: 0.98 requests/s
- Average Response Time: 300.20 ms
- p(95): 384.08 ms
- Max Response Time: 443.75 ms
- Failed Requests: 0%
- Checks Succeeded: 100% (868/868)
- Home page p(95): 281.65 ms
- Recommendation p(95): 390.68 ms
- Result: All thresholds passed. The application maintained stable performance during the 10-minute sustained load without failed requests.

Part B: Reflection Questions
ข้อ 1. Results table A1–A5
Test	Users	Req/s	p(95)	Max	Failed	Checks
A1 Smoke	1	0.44	368.09 ms	372.02 ms	0%	100%
A2 Average	3	1.24	463.58 ms	1.13 s	0%	100%
A3 Stress	5	2.16	374.74 ms	741.37 ms	0%	100%
A4 Spike	5	1.05	381.95 ms	806.37 ms	0%	100%
A5 Soak	2	0.98	384.08 ms	443.75 ms	0%	100%

ข้อ 2. Why was the request rate below 5 req/s with 5 VUs in Step 0.2?
The request rate was below 5 requests per second because each virtual user had to wait for the server response and then sleep for 1 second before starting the next iteration. Therefore, each user could not send requests continuously.

ข้อ 3. Did response time grow from average to stress? Explain the operational envelope.
No. The p(95) response time decreased from 463.58 ms in the average test with 3 VUs to 374.74 ms in the stress test with 5 VUs. Both tests passed all thresholds with 0% failed requests and 100% checks succeeded. In this test environment, the application handled up to 5 virtual users successfully under the tested workload.

ข้อ 4. How long did response time take to recover after the spike?
The response time recovered to approximately its normal level within about 10 seconds after the spike. The p(95) response time increased during the sudden load and then decreased as the number of virtual users returned to one.

ข้อ 5. Did you observe degradation during the soak test? Name one defect that an hours-long soak test could reveal.
The soak test passed all thresholds, with 100% checks succeeded and 0% failed requests. The overall p(95) was 384.08 ms. However, the overall results alone cannot confirm whether performance degraded over time. A longer soak test could reveal memory leaks, where memory usage gradually increases and eventually affects performance.

ข้อ 6. Why use p95 instead of average response time?
The average response time does not show how slow requests can be for users. The p(95) value shows the response time within which 95% of requests are completed. It helps identify slow responses and provides a better understanding of the user experience.

ข้อ 7. Give two reasons why lab system numbers differ from the shared public site over the internet.
1. Network latency: Requests from different locations may take different amounts of time to reach the server and receive responses.
2. Shared resources: The public site may be handling requests from other users at the same time, which can affect response times and throughput.

ข้อ 8. Why should you not run stress, spike, or breakpoint tests with hundreds of users against a site you do not own?
Running a large load test against a site without permission can overload the server, disrupt services, and affect other users. It may also be considered an unauthorized attack. Load tests should only be performed on systems that you own or have written permission to test.

ข้อ 9. Write a vague performance requirement, rewrite it as a testable requirement, and show the K6 thresholds.
**Vague requirement:**

The application should be fast and reliable.

**Testable requirement:**

The application should handle 3 concurrent virtual users for 30 seconds. The 95th percentile response time should be below 1000 ms, the 99th percentile response time should be below 1500 ms, failed requests should be below 1%, and successful checks should be above 99%.

**K6 thresholds:**

```javascript
export const options = {
  vus: 3,
  duration: '30s',
  thresholds: {
    http_req_failed: ['rate<0.01'],
    http_req_duration: [
      'p(95)<1000',
      'p(99)<1500',
    ],
    checks: ['rate>0.99'],
  },
};
```
