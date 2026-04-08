<div align="center">
[![Get $200 DigitalOcean Credit](https://web-platforms.sfo2.cdn.digitaloceanspaces.com/WWW/Badge%203.svg)](https://www.digitalocean.com/?refcode=eddc62174250&utm_campaign=Referral_Invite&utm_medium=Referral_Program&utm_source=badge)
[![Deploy to DO](https://www.deploytodo.com/do-btn-blue.svg)](https://cloud.digitalocean.com/apps/new?repo=https://github.com/iamdgarcia/claude-code-litellm-digitalocean/tree/main)

</div>

# How to Run Claude Code for Free: Setting Up a Gateway with LiteLLM and DigitalOcean

If you use Claude Code, you've likely hit its biggest limitation: token costs. While it's an incredibly powerful tool for AI-assisted development, continuous use and long prompts can drain your wallet fast. 

**This setup solves that.** By leveraging DigitalOcean's current $200 free credit for new users and deploying a self-hosted [LiteLLM](https://www.litellm.ai/) gateway, you can run Claude Code practically for free while gaining total control over your LLM spending, routing, and observability.

## How does it work?

Claude Code supports [third-party gateways](https://code.claude.com/docs/en/llm-gateway) using the Anthropic API format. However, DigitalOcean's Serverless Inference API (which hosts advanced models like Claude 3.5 Sonnet) uses the OpenAI API format (`/v1/chat/completions`).

We bridge this gap using **LiteLLM** hosted on DigitalOcean App Platform. LiteLLM acts as a proxy server, translating Claude Code's requests into a format DigitalOcean understands. 

**Why this setup is better than direct API calls:**
* **Zero Out-of-Pocket Costs:** Pay for your tokens using the $200 DO promotional credit.
* **Cost Tracking & Observability:** See exactly how many tokens you are using in real-time.
* **Provider Agnostic:** Swap backend AI providers in LiteLLM without ever changing your Claude Code workflow.

---

## Products Used

- [Claude Code](https://www.anthropic.com/news/claude-code)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [DigitalOcean Serverless Inference](https://docs.digitalocean.com/products/gradient-ai-platform/how-to/use-serverless-inference/)
- [DigitalOcean App Platform](https://docs.digitalocean.com/products/app-platform/)
- [DigitalOcean Database](https://docs.digitalocean.com/products/databases/)

---

## 🚀 How to Deploy the Gateway

### 1. 1-Click Deploy
Click the button below to deploy the pre-configured LiteLLM gateway directly to DigitalOcean App Platform:

[![Deploy to DO](https://www.deploytodo.com/do-btn-blue.svg)](https://cloud.digitalocean.com/apps/new?repo=https://github.com/iamdgarcia/claude-code-litellm-digitalocean/tree/main)

### 2. Configure Environment Variables
During setup, you will be prompted to set the following environment variables. Leave the rest as default.

> **Tip:** Use `openssl rand -hex 32` in your terminal to generate a secure random master key.

| Environment Variable | Description |
| :--- | :--- |
| `LITELLM_MASTER_KEY` | Master key for authenticating and protecting your LiteLLM gateway. |
| `UI_USERNAME` | Username for accessing the LiteLLM web UI. |
| `UI_PASSWORD` | Password for accessing the LiteLLM web UI. |

*(Optional)* Set `STORE_PROMPTS_IN_SPEND_LOGS=True` to enable storing prompt requests and full responses in your logs.

### 3. Access Your Gateway
Once deployed, browse to your app URL and log in using the credentials you just created: 
`https://your-app-name.ondigitalocean.app/ui`

<img width="2370" height="1593" alt="image" src="https://github.com/user-attachments/assets/023b6e48-c396-4f06-bd76-74360989241e" />

---

## 🔌 Connecting DigitalOcean (The $200 Trick)

Now we need to add the DigitalOcean Serverless Inference API Endpoint to your LiteLLM gateway. [See LiteLLM docs for UI details.](https://docs.litellm.ai/docs/proxy/ui_credentials)

1. Inside the LiteLLM UI, click **Add Model**.
2. Configure it with the following parameters:

* **Provider:** `OpenAI-Compatible Endpoints`
* **LiteLLM Model Name:** `anthropic-claude-4.5-sonnet` *(This is the model name passed to the DO Serverless Inference API)*
* **Model Name:** `digitalocean-anthropic-claude-4.5-sonnet` *(This is your custom name. Claude Code will use this via the `ANTHROPIC_MODEL` env variable)*
* **API Base:** `https://inference.do-ai.run/v1`
* **API Key:** Generate your key here: [DO GenAI Model Access Keys](https://cloud.digitalocean.com/gen-ai/model-access-keys).

<img width="1666" height="1556" alt="image" src="https://github.com/user-attachments/assets/4115cebc-38e0-4474-a1bd-6e4358b5a898" />

---

## 💻 Configuring Claude Code

For security, it's highly recommended to generate a specific Virtual Key for Claude Code rather than using your Master Key.

1. In the LiteLLM web UI, navigate to the **Virtual Keys** tab.
2. Click **Create New Key**.
3. Copy the generated key (`sk-...`).

Now, configure your local environment to point Claude Code to your new gateway. Run these exports in your terminal (or add them to your `~/.zshrc` / `~/.bashrc`):

```bash
export ANTHROPIC_BASE_URL=[https://your-app-name.ondigitalocean.app](https://your-app-name.ondigitalocean.app)
export ANTHROPIC_MODEL=digitalocean-anthropic-claude-4.5-sonnet
export ANTHROPIC_AUTH_TOKEN=your-virtual-key-here
export CLAUDE_CODE_DISABLE_EXPERIMENTAL_BETAS=1
```

Alternatively, you can save this persistently in your Claude Code settings at `~/.claude/settings.json`:

```json
{
  "env": {
    "ANTHROPIC_BASE_URL": "[https://your-app-name.ondigitalocean.app](https://your-app-name.ondigitalocean.app)",
    "ANTHROPIC_MODEL": "digitalocean-anthropic-claude-4.5-sonnet",
    "ANTHROPIC_AUTH_TOKEN": "your-virtual-key-here",
    "CLAUDE_CODE_DISABLE_EXPERIMENTAL_BETAS": "1"
  }
}
```

---

## ✅ Testing the Integration

Start Claude Code in your terminal:

```bash
claude
```

1. You should see your custom model name (`digitalocean-anthropic-claude-4.5-sonnet`) noted at the top of the interface.
2. Send Claude Code a message. It will respond exactly as it normally does.
3. **The Magic:** Go back to your LiteLLM web UI. You will now see your request, response, and token usage logged in real-time, all being paid for by your DigitalOcean credit!
<img width="1920" height="823" alt="image" src="https://github.com/user-attachments/assets/1ece3af4-eaea-4844-a105-e254af65f97e" />
