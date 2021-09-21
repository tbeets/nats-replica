# nats-replica

## Configure
```bash
$ nats str add --config S1.conf
$ nats str add --config S2.conf
$ nats str add --config S3.conf
$ nats str add --config Q1.conf
$ nats pub message.s1.test --count 100 "Message {{Count}}"
$ nats pub message.s2.test --count 100 "Message {{Count}}"
$ nats pub message.s3.test --count 100 "Message {{Count}}"
```

## Check
```bash
$ nats str info Q1
Information for Stream Q1 created 2021-09-20T19:13:20-07:00

Configuration:

     Acknowledgements: true
            Retention: File - WorkQueue
             Replicas: 1
       Discard Policy: New
     Duplicate Window: 2m0s
     Maximum Messages: unlimited
        Maximum Bytes: unlimited
          Maximum Age: 0.00s
 Maximum Message Size: unlimited
    Maximum Consumers: unlimited
              Sources: S1
                       S2


Source Information:

          Stream Name: S1
                  Lag: 0
            Last Seen: 1.25s

          Stream Name: S2
                  Lag: 0
            Last Seen: 0.91s

State:

             Messages: 200
                Bytes: 19 KiB
             FirstSeq: 1 @ 2021-09-21T02:19:16 UTC
              LastSeq: 200 @ 2021-09-21T02:20:14 UTC
     Active Consumers: 0
```

# Consumer (on Q1)
```bash
$ nats con add --config C1.conf
...
Information for Consumer Q1 > C1 created 2021-09-20T19:27:41-07:00

Configuration:

        Durable Name: C1
           Pull Mode: true
      Filter Subject: message.>
      Deliver Policy: All
 Deliver Queue Group: _unset_
          Ack Policy: Explicit
            Ack Wait: 30s
       Replay Policy: Instant
     Max Ack Pending: 20,000
   Max Waiting Pulls: 512

State:

   Last Delivered Message: Consumer sequence: 0 Stream sequence: 0
     Acknowledgment floor: Consumer sequence: 0 Stream sequence: 0
         Outstanding Acks: 0 out of maximum 20000
     Redelivered Messages: 0
     Unprocessed Messages: 200
            Waiting Pulls: 0 of maximum 512
...
$ nats con sub --ack Q1 C1
...
$ nats con sub --ack Q1 C1
...
$ nats con info Q1 C1
Information for Consumer Q1 > C1 created 2021-09-20T19:31:26-07:00

Configuration:

        Durable Name: C1
           Pull Mode: true
      Filter Subject: message.>
      Deliver Policy: All
 Deliver Queue Group: _unset_
          Ack Policy: Explicit
            Ack Wait: 30s
       Replay Policy: Instant
     Max Ack Pending: 20,000
   Max Waiting Pulls: 512

State:

   Last Delivered Message: Consumer sequence: 2 Stream sequence: 2 Last delivery: 1m55s ago
     Acknowledgment floor: Consumer sequence: 2 Stream sequence: 2 Last Ack: 1m55s ago
         Outstanding Acks: 0 out of maximum 20000
     Redelivered Messages: 0
     Unprocessed Messages: 198
            Waiting Pulls: 0 of maximum 512

```