# Downbell heartbeat action

Know when a scheduled GitHub Actions workflow **stops running**: nightly backups, data syncs, scrapers, report jobs.

A failed run is easy to notice. A workflow that silently stops (GitHub disables scheduled workflows in public repos after 60 days without activity; a cron line gets broken; runners have outages) is not. This action pings a [Downbell](https://downbell.app) heartbeat monitor at the end of a successful run. If the ping doesn't arrive on time, Downbell alerts you by phone push, email, Slack, Discord, Telegram or webhook.

Free for up to 50 monitors.

## Usage

1. In Downbell, add a monitor of type **Heartbeat** and set how often the job should run (for example every 24 hours, with a grace period).
2. Copy the heartbeat token from the monitor page and save it as a repository secret named `DOWNBELL_HEARTBEAT`.
3. Add the step at the end of your job:

```yaml
on:
  schedule:
    - cron: '0 2 * * *'

jobs:
  backup:
    runs-on: ubuntu-latest
    steps:
      - run: ./backup.sh
      - uses: downbell/heartbeat-action@v1
        with:
          token: ${{ secrets.DOWNBELL_HEARTBEAT }}
```

The step only runs when the earlier steps succeed, so a failing job also stops the heartbeat and you get alerted.

## Without the action

Any job can ping the URL directly:

```bash
curl -fsS --retry 3 https://downbell.app/h/YOUR_TOKEN
```

## License

MIT
