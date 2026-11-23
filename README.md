# Cline with a Claude subscription instead of an API key

Pick **OpenAI Compatible** as Cline's API Provider, set **Base URL** to `https://api.plan2api.com/v1`, paste a `p2a_` key from [Plan2API](https://plan2api.com/integrations/cline-roo-kilo) and use the Model ID `claude/claude-sonnet-5`: Cline now runs on your Claude Pro or Max subscription instead of a pay-per-token Anthropic API key. The same three fields work in Zoo Code (the maintained Roo Code fork) and Kilo Code, and the same key also serves your ChatGPT Plus or Pro plan as `openai/gpt-6.1-sol`.

**[Get a free `p2a_` key](https://plan2api.com/connect?utm_source=github&utm_medium=readme&utm_campaign=cline-claude-subscription)** | [Cline, Zoo Code and Kilo Code guide on plan2api.com](https://plan2api.com/integrations/cline-roo-kilo) | [API reference](https://plan2api.com/docs)

## Why not Cline's built-in Claude Code provider?

Cline's **Claude Code** provider drives the local `claude` CLI, and Cline's own docs note that responses may not stream token by token and that image uploads and prompt caching are limited in that mode. Plan2API gives Cline a real OpenAI-compatible endpoint on the same subscription: token-by-token streaming, image input, function tool calls passed straight through, and one key that works in every editor on every machine, with Claude, ChatGPT, Gemini and Kimi behind it.

## 1. Turn your Claude subscription into an API key

1. Open [plan2api.com/connect](https://plan2api.com/connect?utm_source=github&utm_medium=readme&utm_campaign=cline-claude-subscription) and sign up with Google.
2. Connect Claude by signing in with your Claude account on Anthropic's own sign-in page. Pro, Max 5x and Max 20x all work.
3. Copy the `p2a_` key you get after connecting. It is shown once; create more on the Keys page (`plan2api.com/dashboard/keys`).

ChatGPT Plus or Pro connects with a one-time device code on OpenAI's own page, Google AI Pro with your Google account, and Kimi Code with its API key.

## 2. Cline

Open Cline's settings (the gear icon in the Cline panel) and fill in:

| Cline field | Value |
| --- | --- |
| API Provider | `OpenAI Compatible` |
| Base URL | `https://api.plan2api.com/v1` |
| OpenAI Compatible API Key | your `p2a_...` key |
| Model ID | `claude/claude-sonnet-5` (Cline loads the list from `/v1/models`; or type it under **Custom Model ID**) |
| Model Configuration > Context Window Size | `1000000` |
| Model Configuration > Max Output Tokens | `128000` |
| Model Configuration > Supports Images | checked |

Keep `/v1` in the Base URL. Leave the price fields at `0`: Plan2API bills no per-token charges, so Cline's cost counter matches what you actually pay.

## 3. Roo Code and Zoo Code

Roo Code shut down on May 15, 2026 and its repository is archived. Zoo Code continues the project (VS Code Marketplace id `ZooCodeOrganization.zoo-code`) and imports your Roo setup: **Export** in Roo Code's settings, **Import Settings** in Zoo Code. Zoo Code removed the old Claude Code CLI provider, so Plan2API is how it runs on a Claude subscription.

Click the gear icon in the Zoo Code panel and fill in:

| Zoo Code field | Value |
| --- | --- |
| API Provider | `OpenAI Compatible` |
| Base URL | `https://api.plan2api.com/v1` |
| API Key | your `p2a_...` key |
| Model | `claude/claude-sonnet-5` |
| Model Configuration > Context Window | `1000000` |
| Model Configuration > Max Output Tokens | `128000`, or `-1` to let the server decide |
| Model Configuration > Image Support | on |

Zoo Code only uses native tool calling, and Plan2API passes function tools through on Claude, Codex, Gemini and Kimi, so every Zoo tool works. The last Roo Code release (3.54.0) uses the same OpenAI Compatible form, so these values work there too.

## 4. Kilo Code

1. Open **Settings** (gear icon) and go to the **Providers** tab.
2. Scroll to the bottom and click **Custom provider**.
3. Fill in:

| Kilo field | Value |
| --- | --- |
| Provider ID | `plan2api` |
| Display name | `Plan2API` |
| Provider API | `OpenAI Compatible` |
| Base URL | `https://api.plan2api.com/v1` |
| API key | your `p2a_...` key |
| Models | pick `claude/claude-sonnet-5` and friends from the fetched list |

4. Click **Submit**. The models appear in Kilo's model picker.

Pick **OpenAI Compatible**, not **OpenAI Responses** or **Anthropic Messages**: Plan2API serves Chat Completions, and the other two fail on every request.

### Kilo config file

Kilo Code (extension and CLI) reads `~/.config/kilo/kilo.json`. Copy [`kilo/kilo.json`](kilo/kilo.json) there and export your key:

```bash
mkdir -p ~/.config/kilo
cp kilo/kilo.json ~/.config/kilo/kilo.json
export PLAN2API_KEY="p2a_your_key"
```

Keep it in the global config: Kilo only resolves `{env:...}` in trusted config, not in a project-level `kilo.json`. Every model in the file carries `limit.context` and `limit.output`, because without them Kilo turns off compaction and caps output at 32,000 tokens.

## Models

| Model ID | Runs on | Context Window Size | Max Output Tokens | Images |
| --- | --- | --- | --- | --- |
| `claude/claude-sonnet-5` | Claude Pro or Max | `1000000` | `128000` | yes |
| `claude/claude-opus-5-5` | Claude Pro or Max | `1000000` | `128000` | yes |
| `claude/claude-haiku-4-5` | Claude Pro or Max | `200000` | `64000` | yes |
| `openai/gpt-6.1-sol` | ChatGPT Plus or Pro (Codex) | `500000` (Codex default) | `128000` | as sent |
| `gemini/gemini-3.8-flash-high` | Google AI Pro or Ultra | `1048576` | not used on `gemini/` ids | yes |
| `kimi/k3` | Kimi Code membership | `1048576` | leave unset | yes |

`kimi/k3` starts on a 256K window and moves to 1M once a conversation passes about 192K tokens, so `1048576` works. `GET /v1/models` lists every Plan2API model, including providers you haven't connected yet; those answer `403 no_credentials`.

## Troubleshooting

| What you see | Why | Fix |
| --- | --- | --- |
| `404` `not_found` | Base URL is missing `/v1` | Use `https://api.plan2api.com/v1` |
| `401` `invalid_api_key` | Key missing, mistyped or revoked, or sent as `x-api-key` by an Anthropic-style provider | Use the OpenAI Compatible provider so the key goes out as `Authorization: Bearer`, and paste the key again |
| `403` `no_credentials` | The model belongs to a provider you haven't connected | Connect it at plan2api.com, or pick a `claude/` model |
| `400` `model_not_found` | Typo or missing prefix | Copy the id exactly, for example `claude/claude-sonnet-5` |
| Kilo fails on every request | **Provider API** is `OpenAI Responses` or `Anthropic Messages` | Edit the provider and pick `OpenAI Compatible` |
| Kilo output stops at 32,000 tokens | `limit.output` is missing for the model | Set `limit.context` and `limit.output` as in `kilo/kilo.json` |
| `400` `context_length_exceeded` | The context window you entered is bigger than the model's real window | Use the values in the models table |
| `429` `subscription_quota_exhausted` | Your Claude plan hit its 5-hour or weekly limit | Wait for the `Retry-After` time, or connect a second Claude account so requests fail over automatically |
| `429` `rate` or `inflight` | Agent bursts beyond the Free plan's 1 request per second (bursts of 20, 4 at once) | Move to Pro: 10 per second, bursts of 600, 16 at once |

## FAQ

### Can I use my Claude Pro or Max subscription in Cline?

Yes. Connect it to Plan2API, then choose the OpenAI Compatible provider in Cline with Base URL `https://api.plan2api.com/v1`, your `p2a_` key and Model ID `claude/claude-sonnet-5`.

### How do I use a Claude subscription instead of an Anthropic API key in Cline?

Skip the Anthropic provider, which wants a Console API key billed per token. Plan2API signs in with your Claude account on Anthropic's own page and hands Cline a `p2a_` key for the OpenAI Compatible provider.

### Can I use ChatGPT Plus in Cline?

Yes. Connect ChatGPT in Plan2API and set the Model ID to `openai/gpt-6.1-sol`. GPT sits behind the same OpenAI Compatible settings as Claude, so switching models is just a Model ID change.

### Is Roo Code still available?

No. Roo Code shut down on May 15, 2026 and its repository is archived. Zoo Code continues it and imports Roo settings.

### How do I connect a Claude Max subscription to Roo Code or Zoo Code?

Connect Claude Max to Plan2API, then use the OpenAI Compatible provider with Base URL `https://api.plan2api.com/v1`, your `p2a_` key and Model `claude/claude-sonnet-5`. Zoo Code dropped the Claude Code CLI provider, so this is the subscription route.

### Does the Cline Base URL need /v1?

Yes. Enter `https://api.plan2api.com/v1`.

### Which Provider API should I pick in Kilo Code?

`OpenAI Compatible`. Plan2API serves Chat Completions, not the Responses or Anthropic Messages APIs.

### Do tool calls work through Plan2API?

Yes. Function tools and `tool_choice` pass through on Claude, Codex, Gemini and Kimi, which is what Cline, Zoo Code and Kilo Code rely on.

### What does it cost?

Plan2API Free is $0 with one connected subscription and no card. Pro is $12 a month for 3 subscriptions and agent-friendly rate limits (10 per second, 16 at once); Scale is $29 a month for 25. See [pricing](https://plan2api.com/pricing).

## More guides

- [Cline, Zoo Code and Kilo Code + Plan2API guide](https://plan2api.com/integrations/cline-roo-kilo)
- [Use your Claude subscription as an API](https://plan2api.com/claude-subscription-api)
- [Claude Pro and Max usage limits](https://plan2api.com/claude-usage-limits)
- [Continue + Plan2API](https://plan2api.com/integrations/continue)
- [Cursor + Plan2API](https://plan2api.com/integrations/cursor)
- [Plan2API API reference](https://plan2api.com/docs)

Same setup, other clients:

- [OpenCode with a Claude Pro or Max subscription](https://github.com/bitgate/opencode-claude-subscription)
- [SillyTavern with a Claude subscription](https://github.com/bitgate/sillytavern-claude-subscription)
- [Janitor AI Claude proxy](https://github.com/bitgate/janitor-ai-claude-proxy)
- [Open WebUI with a ChatGPT subscription](https://github.com/bitgate/open-webui-chatgpt-subscription)
