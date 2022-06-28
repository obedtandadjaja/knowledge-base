# Rate limit algorithms

## Leaky bucket

Idea is that the bucket's leak matches the rate of which water should come in. If the rate of water is more than accounted for then it will leak.

Leaky bucket helps with smoothing out bursty traffic. Bursty chunks are stored in the bucket and sent out at an average rate.

Implementation example using pointers and thread sleeps: https://github.com/uber-go/ratelimit/blob/main/limiter_atomic.go

## Token bucket

Idea is that there is a fixed capacity buckets where tokens are added at a fixed rate. Tokens are removed when objects enter the bucket. If not enough token is sufficient for the object to enter the bucket then the object will be dropped.

A conforming flow can thus contain traffic with an average rate up to the rate at which tokens are added to the bucket, and have a burstiness determined by the depth of the bucket.

https://en.wikipedia.org/wiki/Token_bucket
