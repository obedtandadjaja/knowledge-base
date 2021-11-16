# Cloud Spanner TrueTime

Resource: https://cloud.google.com/spanner/docs/true-time-external-consistency

TrueTime is a highly available, distributed clock that is provided to applications on all Google servers.

- Generates monotonically increasing timestamps.
  - This guarantee holds across all servers and all timestamps.
- Used to assign timestamps to transactions. 
- Generates timestamps without the need for global communication.
- Uses timestamps for strong reads, even reads from multiple servers.

## MVCC and timestamp

Timestamps and multi-version concurrency control allows enables consistent reads across an entire database (even across Cloud regions) without blocking writes.

- Keep multiple immutable versions of data.
- Write - creates a new immutable version whose timestamp is that of the write's transaction.
- Snapshot read at a timestamp returns the value of the most recent version prior to the timestamp.
- It is crucial that timestamps assigned to versions are consistent with the order in which transactions can be observed to commit.

## TrueTime - how it works

Resource: https://static.googleusercontent.com/media/research.google.com/en//archive/spanner-osdi2012.pdf

TrueTime's functions:

- now() - returns an interval of time [earliest, latest].
- after(t) - true if t has definitely passed
- before(t) - true if t has definitely not arrived

Infrastructure:

- The underlying time references used by TrueTime are GPS and atomic clocks. 
  - TrueTime uses two forms of time reference because they have different failure modes. GCPS can fail from antenna and receiver failures, local radio interference, and GPS system outages. Atomic clock can fail in ways uncorrelated to GPS.
- TrueTime is implemented by a set of time master machines per datacenter and a timeslave daemon per machine.
  - Most masters have GPS receivers with dedicated antennas and are separated physically to reduce effects of antenna failures, radio interference, and spoofing.
  - Remaining masters have atomic clocks.
- All masters' time references are regularly compared against each other.
  - Checks if its reference and its own time has differed by much, if so then it evicts itself.
- Every daemon polls a variety of masters to reduce vulnerability to errors from any one master.
  - Uses a variant of Marzullo's algorithm to detect and reject liars and synchronize the local machine clocks to the non-liars.
  - Machines that frequently are out of sync will get evicted.
  - Poll interval is 30 seconds
