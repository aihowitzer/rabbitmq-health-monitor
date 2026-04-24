# rabbitmq-health-monitor

> Audit your RabbitMQ cluster in 60 seconds. Find silent failures before they find you.

[![Node.js](https://img.shields.io/badge/Node.js-18+-339933?style=flat-square&logo=node.js&logoColor=white)](https://nodejs.org)
[![RabbitMQ](https://img.shields.io/badge/RabbitMQ-3.x+-FF6600?style=flat-square&logo=rabbitmq&logoColor=white)](https://rabbitmq.com)
[![License](https://img.shields.io/badge/license-MIT-blue?style=flat-square)](LICENSE)
[![Author](https://img.shields.io/badge/by-aihowitzer.com-FF6A00?style=flat-square)](https://aihowitzer.com)

---

## The problem

Most RabbitMQ setups look fine — until they don't.

Messages disappear silently. Dead letter queues fill up unnoticed. One slow consumer starves the entire pipeline. Retry policies are missing on half your consumers. And nobody finds out until a customer complains or revenue takes a hit.

This tool gives you a **full health report in 60 seconds** — no manual poking around the management UI, no guesswork.

---

## What it checks

```
Queue Health
  ✓ Message accumulation rate
  ✓ Dead letter queue depth + overflow detection
  ✓ Consumer count vs queue depth ratio
  ✓ Idle queues with pending messages

Retry & Reliability
  ✓ Missing retry policies on consumers
  ✓ Missing or undefined message TTL
  ✓ Unacked message buildup
  ✓ Prefetch count misconfigurations

Observability
  ✓ Structured logging presence
  ✓ Alert configuration status
  ✓ Connection churn rate
  ✓ Channel leak detection

Performance
  ✓ Publish vs consume rate delta
  ✓ Memory high-watermark proximity
  ✓ Disk free alarm status
  ✓ Node cluster health
```

---

## Quickstart

```bash
# Install
npm install -g rabbitmq-health-monitor

# Run against your cluster
rabbitmq-monitor --host=localhost --port=15672 --user=admin --pass=yourpassword

# Or with a config file
rabbitmq-monitor --config=./rmq-config.json
```

---

## Sample output

```bash
$ rabbitmq-monitor --host=your-cluster --port=15672

  ██████╗ ███╗   ███╗ ██████╗
  ██╔══██╗████╗ ████║██╔═══██╗
  ██████╔╝██╔████╔██║██║   ██║
  ██╔══██╗██║╚██╔╝██║██║▄▄ ██║
  ██║  ██║██║ ╚═╝ ██║╚██████╔╝
  ╚═╝  ╚═╝╚═╝     ╚═╝ ╚══▀▀═╝  health monitor v1.0.0

[■■■■■■■■■■] scanning 12 queues across 1 vhost...

QUEUE HEALTH
  ⚠  orders.processing       : 4,821 messages, 0 active consumers
  ⚠  notifications.email     : depth growing +340/min, consumers falling behind
  ✓  payments.confirmed      : healthy
  ✓  events.audit            : healthy

DEAD LETTER QUEUES
  ✗  orders.processing.dlq   : 12,443 messages — OVERFLOW WARNING
  ✗  webhooks.outbound.dlq   : 891 messages — unmonitored
  ✓  payments.failed.dlq     : 0 messages

RETRY POLICIES
  ✗  notifications.consumer  : no retry policy configured
  ✗  webhooks.consumer       : no retry policy configured
  ✗  events.consumer         : no retry policy configured
  ✓  payments.consumer       : 3 retries, exponential backoff ✓

MESSAGE TTL
  ✗  orders.processing       : ttl=undefined — silent drops possible
  ✗  notifications.email     : ttl=undefined — silent drops possible
  ✓  payments.confirmed      : ttl=86400000ms

OBSERVABILITY
  ✗  structured logging      : not detected on 3 of 4 consumers
  ✗  azure monitor alerts    : not configured
  ⚠  connection churn        : 42 reconnects in last 1h

──────────────────────────────────────────────
SEVERITY    CRITICAL
ISSUES      9 found (4 critical, 3 warning, 2 info)
LOSS RATE   ~18% estimated message loss
REPORT      ./audit-2026-04-24T10-33-00.json
──────────────────────────────────────────────

→ Run with --fix-suggestions to see recommended fixes
→ Run with --export=html to generate a shareable report
```

---

## Config file

```json
{
  "host": "your-rmq-host",
  "port": 15672,
  "username": "admin",
  "password": "yourpassword",
  "vhost": "/",
  "thresholds": {
    "dlq_depth_warning": 100,
    "dlq_depth_critical": 1000,
    "consumer_utilisation_warning": 0.5,
    "message_rate_delta_warning": 200
  },
  "alerts": {
    "email": "connect@aihowitzer.com",
    "webhook": "https://your-slack-webhook"
  }
}
```

---

## Output formats

```bash
# Terminal (default)
rabbitmq-monitor --host=localhost

# JSON report
rabbitmq-monitor --host=localhost --output=json > report.json

# HTML report (shareable)
rabbitmq-monitor --host=localhost --output=html > report.html

# CI mode — exits with code 1 if critical issues found
rabbitmq-monitor --host=localhost --ci
```

---

## Use in CI/CD

```yaml
# .github/workflows/rmq-health.yml
- name: RabbitMQ Health Check
  run: |
    npx rabbitmq-health-monitor \
      --host=${{ secrets.RMQ_HOST }} \
      --user=${{ secrets.RMQ_USER }} \
      --pass=${{ secrets.RMQ_PASS }} \
      --ci
```

---

## Roadmap

- [ ] Prometheus metrics export
- [ ] Grafana dashboard template
- [ ] Slack / Teams / WhatsApp alerts
- [ ] Azure Service Bus support
- [ ] Apache Kafka adapter
- [ ] Web UI dashboard

---

## Found critical issues in your cluster?

This tool tells you **what** is broken. If you need someone to fix it — that's what I do.

**[→ Book a free 30-min audit at aihowitzer.com](https://aihowitzer.com)**

I'll review your specific setup, explain every issue in plain terms, and give you a fixed-price quote to sort it out.

---

## License

MIT © [AI Howitzer](https://aihowitzer.com)
