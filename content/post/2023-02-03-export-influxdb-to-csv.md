---
title: "TIL: Export InfluxDB data to CSV"
date: 2023-02-03
---

Recently, I had to export a larger amount of data from our InfluxDB for one of our customer and due to the size of 
data I was unable to use Chronograf. Fortunately for me, there is a build in `influx` command that allows you to 
execute a query and export it to csv. The whole command looks like:

```bash
influx -host <host-name> \
       -ssl \
       -port <port> \
       -database <database> \
       -username <user-name> \
       -password <password> \
       -execute "SELECT pressure FROM <measurement> WHERE \"device_id\" = device-id" \
       -format 'csv' 
       -precision 'rfc3339' > output.csv
```

