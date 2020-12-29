## Google Handbook

> The four golden signals of monitoring are latency, traffic, errors, and saturation. If you can only measure four metrics of your user-facing system, focus on these four.

- Latency — The time it takes to service a request
- Traffic — A measure of how much demand is being placed on your system
- Errors — The rate of requests that fail
- Saturation — How “full” your service is

## USE Method

> Resource: all physical server functional components (CPUs, disks, busses, …)

- Utilization: the average time that the resource was busy servicing work
- Saturation: the degree to which the resource has extra work which it can’t service, often queued
- Errors: the count of error events

Source: http://www.brendangregg.com/usemethod.html

## RED Method

- Rate: The number of requests per second.
- Errors: The number of those requests that are failing.
- Duration: The amount of time those requests take.

Source: https://www.youtube.com/watch?v=TJLpYXbnfQ4

## USE vs RED

> “The USE method is for resources and the RED method is for my services” — Tom Wilkie
