# Sensorhub AI tools

Free Claude skills that turn your Sensorhub data into reports you can read, share and act on. Give Claude a company name and it does the rest.

| Tool | What you get |
| --- | --- |
| `reddit-fit-score` | An A-F grade for how well Reddit fits as a channel for the company, with a six-part score breakdown, the top communities and live threads. One HTML file. |
| `reddit-sov-report` | Reddit share of voice against competitors, which subreddits matter, and the threads AI answers are likely to cite. A Word document and an HTML report. |
| `brand-mention-report` | How the brand is discussed across Reddit, LinkedIn and X in the last 30 days, sorted by intent, with a list of threads worth replying to. An HTML file and a PDF. |
| `signal-analysis-report` | A full competitive intel report: who the buyers are, where competitors lose them, and the 10 conversations worth joining first. A Word document. |

## Before you start

1. A Sensorhub account with the company set up as a project. The tools read only your Sensorhub data, so a company that isn't tracked can't be graded.
2. Sensorhub connected to Claude. In Sensorhub, open **MCP** in your workspace settings. It shows the address and the steps for Claude Code, Claude desktop and Claude.ai.

## Install

**Claude Code**

```
/plugin marketplace add SensorhubAI/sensorhub-ai-tools
/plugin install sensorhub-tools@sensorhub-ai-tools
```

**Claude.ai and Claude desktop**

Go to Customize > Plugins > Add > Add marketplace > Add from a repository, enter `SensorhubAI/sensorhub-ai-tools`, then install `sensorhub-tools`.

## Use

Ask in plain words, for example:

- "Run a Reddit Fit Score for Acme"
- "Brand mentions report for Acme"
- "Reddit share of voice report for Acme"
- "Signal analysis report for Acme"

Or pick a tool from the `/` menu, such as `/sensorhub-tools:reddit-fit-score`.

The Word reports need Claude's docx skill or the `docx` npm package. The brand mentions PDF needs `weasyprint`. Claude installs these when it can.

## How the numbers work

Every figure comes from a Sensorhub signal. The Reddit Fit Score and brand mentions report never use web search. The share of voice and signal analysis reports also use web search to fill gaps the project data doesn't cover.
