# Grafana

## Build dashboards

Simply look for a dashboard and click on the plus icon to the left in the main menu. Then you chose *import* and enter the dashbaords id.

Sources:

- <https://grafana.com/grafana/dashboards/9614>
- <https://grafana.com/grafana/dashboards/9632>

## Overriding time interval in graphics

Sources:

- <https://grafana.com/docs/grafana/latest/dashboards/time-range-controls/#panel-time-overrides-and-timeshift>

## Format datetime in tables

Sources:

- <https://grafana.com/docs/grafana/latest/administration/configuration/#date_formats>
- <https://github.com/grafana/grafana/issues/24917#issuecomment-685690047>

## Timezone in datepicker

Grafana changes the choosen timerange in datapicker based on the local timezone of the browser. e.g. If I choose last month and my local browser time is CEST then Grafana will move the generated timestamp by 2 hours (if it is using UTC). So

```sql
BETWEEN '2022-03-01T00:00:00Z' AND '2022-03-31T23:59:59.999Z'
```

becomes

```sql
BETWEEN '2022-02-28T23:00:00Z' AND '2022-03-31T21:59:59.999Z'
```

Sources:

- <https://github.com/grafana/grafana/issues/13769>