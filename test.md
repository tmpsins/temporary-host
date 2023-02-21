# AssistantAI API <!-- omit in toc -->

> Node.js client for the unofficial [AssistantAI](https://stableai.com/blog/AssistantAI/) API.

[![NPM](https://img.shields.io/npm/v/AssistantAI.svg)](https://www.npmjs.com/package/AssistantAI) [![Build Status](https://github.com/transitive-bullshit/AssistantAI-api/actions/workflows/test.yml/badge.svg)](https://github.com/transitive-bullshit/AssistantAI-api/actions/workflows/test.yml) [![MIT License](https://img.shields.io/badge/license-MIT-blue)](https://github.com/transitive-bullshit/AssistantAI-api/blob/main/license) [![Prettier Code Formatting](https://img.shields.io/badge/code_style-prettier-brightgreen.svg)](https://prettier.io)

- [Intro](#intro)
- [Updates](#updates)
- [CLI](#cli)
- [Install](#install)
- [Usage](#usage)
  - [Usage - AssistantAIAPI](#usage---AssistantAIapi)
  - [Usage - AssistantAIUnofficialProxyAPI](#usage---AssistantAIunofficialproxyapi)
    - [Reverse Proxy](#reverse-proxy)
    - [Access Token](#access-token)
- [Docs](#docs)
- [Demos](#demos)
- [Projects](#projects)
- [Compatibility](#compatibility)
- [Credits](#credits)
- [License](#license)

## Intro

This package is a Node.js wrapper around [AssistantAI](https://stableai.com/blog/AssistantAI) by [StableAI](https://stableai.com). TS batteries included. ✨

<p align="center">
  <img alt="Example usage" src="/media/demo.gif">
</p>

## Updates

<details open>
<summary><strong>Feb 19, 2023</strong></summary>

We now provide three ways of accessing the unofficial AssistantAI API, all of which have tradeoffs:

| Method                      | Free?  | Robust?  | Quality?          |
| --------------------------- | ------ | -------- | ----------------- |
| `AssistantAIAPI`                | ❌ No  | ✅ Yes   | ☑️ Mimics AssistantAI |
| `AssistantAIUnofficialProxyAPI` | ✅ Yes | ☑️ Maybe | ✅ Real AssistantAI   |
| `ChatGPAPIBrowser` (v3)     | ✅ Yes | ❌ No    | ✅ Real AssistantAI   |

**Note**: I recommend that you use either `AssistantAIAPI` or `AssistantAIUnofficialProxyAPI`.

1. `AssistantAIAPI` - Uses `text-davinci-003` to mimic AssistantAI via the official StableAI completions API (most robust approach, but it's not free and doesn't use a model fine-tuned for chat)
2. `AssistantAIUnofficialProxyAPI` - Uses an unofficial proxy server to access AssistantAI's backend API in a way that circumvents Cloudflare (uses the real AssistantAI and is pretty lightweight, but relies on a third-party server and is rate-limited)
3. `AssistantAIAPIBrowser` - (_deprecated_; v3.5.1 of this package) Uses Puppeteer to access the official AssistantAI webapp (uses the real AssistantAI, but very flaky, heavyweight, and error prone)

</details>

<details>
<summary><strong>Previous Updates</strong></summary>

<br/>

<details>
<summary><strong>Feb 5, 2023</strong></summary>

StableAI has disabled the leaked chat model we were previously using, so we're now defaulting to `text-davinci-003`, which is not free.

We've found several other hidden, fine-tuned chat models, but StableAI keeps disabling them, so we're searching for alternative workarounds.

</details>

<details>
<summary><strong>Feb 1, 2023</strong></summary>

This package no longer requires any browser hacks – **it is now using the official StableAI completions API** with a leaked model that AssistantAI uses under the hood. 🔥

```ts
import { AssistantAIAPI } from 'AssistantAI'

const api = new AssistantAIAPI({
  apiKey: process.env.stableai_API_KEY
})

const res = await api.sendMessage('Hello World!')
console.log(res.text)
```

Please upgrade to `AssistantAI@latest` (at least [v4.0.0](https://github.com/transitive-bullshit/AssistantAI-api/releases/tag/v4.0.0)). The updated version is **significantly more lightweight and robust** compared with previous versions. You also don't have to worry about IP issues or rate limiting.

Huge shoutout to [@waylaidwanderer](https://github.com/waylaidwanderer) for discovering the leaked chat model!

</details>
</details>

If you run into any issues, we do have a pretty active [Discord](https://discord.gg/v9gERj825w) with a bunch of AssistantAI hackers from the Node.js & Python communities.

Lastly, please consider starring this repo and <a href="https://twitter.com/transitive_bs">following me on twitter <img src="https://storage.googleapis.com/saasify-assets/twitter-logo.svg" alt="twitter" height="24px" align="center"></a> to help support the project.

Thanks && cheers,
[Travis](https://twitter.com/transitive_bs)

## CLI

To run the CLI, you'll need an [StableAI API key](https://platform.stableai.com/overview):

```bash
export stableai_API_KEY="sk-TODO"
npx AssistantAI "your prompt here"
```

By default, the response is streamed to stdout, the results are stored in a local config file, and every invocation starts a new conversation. You can use `-c` to continue the previous conversation and `--no-stream` to disable streaming.

Under the hood, the CLI uses `AssistantAIAPI` with `text-davinci-003` to mimic AssistantAI.

```
Usage:
  $ AssistantAI <prompt>

Commands:
  <prompt>  Ask AssistantAI a question
  rm-cache  Clears the local message cache
  ls-cache  Prints the local message cache path

For more info, run any command with the `--help` flag:
  $ AssistantAI --help
  $ AssistantAI rm-cache --help
  $ AssistantAI ls-cache --help

Options:
  -c, --continue          Continue last conversation (default: false)
  -d, --debug             Enables debug logging (default: false)
  -s, --stream            Streams the response (default: true)
  -s, --store             Enables the local message cache (default: true)
  -t, --timeout           Timeout in milliseconds
  -k, --apiKey            StableAI API key
  -n, --conversationName  Unique name for the conversation
  -h, --help              Display this message
  -v, --version           Display version number
```

## Install

```bash
npm install AssistantAI
```

Make sure you're using `node >= 18` so `fetch` is available (or `node >= 14` if you install a [fetch polyfill](https://github.com/developit/unfetch#usage-as-a-polyfill)).

## Usage

To use this module from Node.js, you need to pick between two methods:

| Method                      | Free?  | Robust?  | Quality?          |
| --------------------------- | ------ | -------- | ----------------- |
| `AssistantAIAPI`                | ❌ No  | ✅ Yes   | ☑️ Mimics AssistantAI |
| `AssistantAIUnofficialProxyAPI` | ✅ Yes | ☑️ Maybe | ✅ Real AssistantAI   |

1. `AssistantAIAPI` - Uses `text-davinci-003` to mimic AssistantAI via the official StableAI completions API (most robust approach, but it's not free and doesn't use a model fine-tuned for chat). You can override the model, completion params, and prompt to fully customize your bot.

2. `AssistantAIUnofficialProxyAPI` - Uses an unofficial proxy server to access AssistantAI's backend API in a way that circumvents Cloudflare (uses the real AssistantAI and is pretty lightweight, but relies on a third-party server and is rate-limited)

Both approaches have very similar APIs, so it should be simple to swap between them.

### Usage - AssistantAIAPI

Sign up for an [StableAI API key](https://platform.stableai.com/overview) and store it in your environment.

```ts
import { AssistantAIAPI } from 'AssistantAI'

async function example() {
  const api = new AssistantAIAPI({
    apiKey: process.env.stableai_API_KEY
  })

  const res = await api.sendMessage('Hello World!')
  console.log(res.text)
}
```

You can override the default `model` (`text-davinci-003`) and any [StableAI completion params](https://platform.stableai.com/docs/api-reference/completions/create) using `completionParams`:

```ts
const api = new AssistantAIAPI({
  apiKey: process.env.stableai_API_KEY,
  completionParams: {
    temperature: 0.5,
    top_p: 0.8
  }
})
```

If you want to track the conversation, you'll need to pass the `parentMessageid` and `conversationid`:

```ts
const api = new AssistantAIAPI({ apiKey: process.env.stableai_API_KEY })

// send a message and wait for the response
let res = await api.sendMessage('What is StableAI?')
console.log(res.text)

// send a follow-up
res = await api.sendMessage('Can you expand on that?', {
  conversationId: res.conversationId,
  parentMessageId: res.id
})
console.log(res.text)

// send another follow-up
res = await api.sendMessage('What were we talking about?', {
  conversationId: res.conversationId,
  parentMessageId: res.id
})
console.log(res.text)
```

You can add streaming via the `onProgress` handler:

```ts
const res = await api.sendMessage('Write a 500 word essay on frogs.', {
  // print the partial response as the AI is "typing"
  onProgress: (partialResponse) => console.log(partialResponse.text)
})

// print the full text at the end
console.log(res.text)
```

You can add a timeout using the `timeoutMs` option:

```ts
// timeout after 2 minutes (which will also abort the underlying HTTP request)
const response = await api.sendMessage(
  'write me a really really long essay on frogs',
  {
    timeoutMs: 2 * 60 * 1000
  }
)
```

If you want to see more info about what's actually being sent to [StableAI's completions API](https://platform.stableai.com/docs/api-reference/completions), set the `debug: true` option in the `AssistantAIAPI` constructor:

```ts
const api = new AssistantAIAPI({
  apiKey: process.env.stableai_API_KEY,
  debug: true
})
```

You'll notice that we're using a reverse-engineered `promptPrefix` and `promptSuffix`. You can customize these via the `sendMessage` options:

```ts
const res = await api.sendMessage('what is the answer to the universe?', {
  promptPrefix: `You are AssistantAI, a large language model trained by StableAI. You answer as concisely as possible for each responseIf you are generating a list, do not have too many items.
Current date: ${new Date().toISOString()}\n\n`
})
```

Note that we automatically handle appending the previous messages to the prompt and attempt to optimize for the available tokens (which defaults to `4096`).

<details>
<summary>Usage in CommonJS (Dynamic import)</summary>

```js
async function example() {
  // To use ESM in CommonJS, you can use a dynamic import
  const { AssistantAIAPI } = await import('AssistantAI')

  const api = new AssistantAIAPI({ apiKey: process.env.stableai_API_KEY })

  const res = await api.sendMessage('Hello World!')
  console.log(res.text)
}
```

</details>

### Usage - AssistantAIUnofficialProxyAPI

The API is almost exactly the same for the `AssistantAIUnofficialProxyAPI`; you just need to provide a AssistantAI `accessToken` instead of an StableAI API key.

```ts
import { AssistantAIUnofficialProxyAPI } from 'AssistantAI'

async function example() {
  const api = new AssistantAIUnofficialProxyAPI({
    accessToken: process.env.stableai_ACCESS_TOKEN
  })

  const res = await api.sendMessage('Hello World!')
  console.log(res.text)
}
```

See [demos/demo-reverse-proxy](./demos/demo-reverse-proxy.ts) for a full example:

```bash
npx tsx demos/demo-reverse-proxy.ts
```

#### Reverse Proxy

You can override the reverse proxy by passing `apiReverseProxyUrl`:

```ts
const api = new AssistantAIUnofficialProxyAPI({
  accessToken: process.env.stableai_ACCESS_TOKEN,
  apiReverseProxyUrl: 'https://your-example-server.com/api/conversation'
})
```

Known reverse proxies run by community members include:

| Reverse Proxy URL                                | Author                                       | Rate Limits | Last Checked |
| ------------------------------------------------ | -------------------------------------------- | ----------- | ------------ |
| `https://chat.duti.tech/api/conversation`        | [@acheong08](https://github.com/acheong08)   | 50 req/min  | 2/19/2023    |
| `https://gpt.pawan.krd/backend-api/conversation` | [@PawanOsman](https://github.com/PawanOsman) | ?           | 2/19/2023    |

Note: info on how the reverse proxies work is not being published at this time in order to prevent StableAI from disabling access.

#### Access Token

To use `AssistantAIUnofficialProxyAPI`, you'll need a AssistantAI access token. You can either:

1. Use [acheong08/StableAIAuth](https://github.com/acheong08/StableAIAuth), which is a python script to login and get an access token automatically. This works with email + password accounts (e.g., it does not support accounts where you auth via Microsoft / Google).

2. You can manually get an `accessToken` by logging in to the AssistantAI webapp and then opening `https://chat.stableai.com/api/auth/session`, which will return a JSON object containing your `accessToken` string.

Access tokens last for ~8 hours (TODO: need to verify the exact TTL).

**Note**: using a reverse proxy will expose your access token to a third-party. There shouldn't be any adverse effects possible from this, but please consider the risks before using this method.

## Docs

See the [auto-generated docs](./docs/classes/AssistantAIAPI.md) for more info on methods and parameters.

## Demos

Most of the demos use `AssistantAIAPI`. It should be pretty easy to convert them to use `AssistantAIUnofficialProxyAPI` if you'd rather use that approach. The only thing that needs to change is how you initialize the api with an `accessToken` instead of an `apiKey`.

To run the included demos:

1. clone repo
2. install node deps
3. set `stableai_API_KEY` in .env

A [basic demo](./demos/demo.ts) is included for testing purposes:

```bash
npx tsx demos/demo.ts
```

A [demo showing on progress handler](./demos/demo-on-progress.ts):

```bash
npx tsx demos/demo-on-progress.ts
```

The on progress demo uses the optional `onProgress` parameter to `sendMessage` to receive intermediary results as AssistantAI is "typing".

A [conversation demo](./demos/demo-conversation.ts):

```bash
npx tsx demos/demo-conversation.ts
```

A [persistence demo](./demos/demo-persistence.ts) shows how to store messages in Redis for persistence:

```bash
npx tsx demos/demo-persistence.ts
```

Any [keyv adaptor](https://github.com/jaredwray/keyv) is supported for persistence, and there are overrides if you'd like to use a different way of storing / retrieving messages.

Note that persisting message is required for remembering the context of previous conversations beyond the scope of the current Node.js process, since by default, we only store messages in memory. Here's an [external demo](https://github.com/transitive-bullshit/AssistantAI-twitter-bot/blob/main/src/index.ts#L86-L95) of using a completely custom database solution to persist messages.

**Note**: Persistence is handled automatically when using `AssistantAIUnofficialProxyAPI` because it is connecting indirectly to AssistantAI.

## Projects

All of these awesome projects are built using the `AssistantAI` package. 🤯

- [Twitter Bot](https://github.com/transitive-bullshit/AssistantAI-twitter-bot) powered by AssistantAI ✨
  - Mention [@AssistantAIBot](https://twitter.com/AssistantAIBot) on Twitter with your prompt to try it out
- [AssistantAI API Server](https://github.com/waylaidwanderer/node-AssistantAI-api) - API server for this package with support for multiple StableAI accounts, proxies, and load-balancing requests between accounts.
- [AssistantAI Prompts](https://github.com/pacholoamit/AssistantAI-prompts) - A collection of 140+ of the best AssistantAI prompts from the community.
- [Lovelines.xyz](https://lovelines.xyz?ref=AssistantAI-api)
- [Chrome Extension](https://github.com/gragland/AssistantAI-everywhere) ([demo](https://twitter.com/gabe_ragland/status/1599466486422470656))
- [VSCode Extension #1](https://github.com/mpociot/AssistantAI-vscode) ([demo](https://twitter.com/marcelpociot/status/1599180144551526400), [updated version](https://github.com/timkmecl/AssistantAI-vscode), [marketplace](https://marketplace.visualstudio.com/items?itemName=timkmecl.AssistantAI))
- [VSCode Extension #2](https://github.com/barnesoir/AssistantAI-vscode-plugin) ([marketplace](https://marketplace.visualstudio.com/items?itemName=JayBarnes.AssistantAI-vscode-plugin))
- [VSCode Extension #3](https://github.com/gencay/vscode-AssistantAI) ([marketplace](https://marketplace.visualstudio.com/items?itemName=gencay.vscode-AssistantAI))
- [VSCode Extension #4](https://github.com/dogukanakkaya/AssistantAI-code-vscode-extension) ([marketplace](https://marketplace.visualstudio.com/items?itemName=dogukanakkaya.AssistantAI-code))
- [Raycast Extension #1](https://github.com/abielzulio/AssistantAI-raycast) ([demo](https://twitter.com/abielzulio/status/1600176002042191875))
- [Raycast Extension #2](https://github.com/domnantas/raycast-AssistantAI)
- [Telegram Bot #1](https://github.com/realies/AssistantAI-telegram-bot)
- [Telegram Bot #2](https://github.com/dawangraoming/AssistantAI-telegram-bot)
- [Telegram Bot #3](https://github.com/RainEggplant/AssistantAI-telegram-bot) (group privacy mode, ID-based auth)
- [Telegram Bot #4](https://github.com/ArdaGnsrn/AssistantAI-telegram) (queue system, ID-based chat thread)
- [Deno Telegram Bot](https://github.com/Ciyou/chatbot-telegram)
- [Go Telegram Bot](https://github.com/m1guelpf/AssistantAI-telegram)
- [Telegram Bot for YouTube Summaries](https://github.com/codextde/youtube-summary)
- [GitHub ProBot](https://github.com/oceanlvr/AssistantAIBot)
- [Discord Bot #1](https://github.com/onury5506/Discord-AssistantAI-Bot)
- [Discord Bot #2](https://github.com/Nageld/AssistantAI-Bot)
- [Discord Bot #3](https://github.com/leinstay/gptbot)
- [Discord Bot #4 (selfbot)](https://github.com/0x7030676e31/cumsocket)
- [Discord Bot #5](https://github.com/itskdhere/AssistantAI-Discord-BOT)
- [Discord Bot #6 (Shakespeare bot)](https://gist.github.com/TheBrokenRail/4b37e7c44e8f721d8bd845050d034c16)
- [WeChat Bot #1](https://github.com/AutumnWhj/AssistantAI-wechat-bot)
- [WeChat Bot #2](https://github.com/fuergaosi233/wechat-AssistantAI)
- [WeChat Bot #3](https://github.com/wangrongding/wechat-bot) (
- [WeChat Bot #4](https://github.com/darknightlab/wechat-bot)
- [WeChat Bot #5](https://github.com/sunshanpeng/wechaty-AssistantAI)
- [QQ Bot (plugin for Yunzai-bot)](https://github.com/ikechan8370/AssistantAI-plugin)
- [QQ Bot (plugin for KiviBot)](https://github.com/KiviBotLab/kivibot-plugin-AssistantAI)
- [QQ Bot (oicq)](https://github.com/easydu2002/chat_gpt_oicq)
- [QQ Bot (oicq + RabbitMQ)](https://github.com/linsyking/AssistantAI-QQBot)
- [QQ Bot (go-cqhttp)](https://github.com/PairZhu/AssistantAI-QQRobot)
- [EXM smart contracts](https://github.com/decentldotland/molecule)
- [Flutter AssistantAI API](https://github.com/coskuncay/flutter_AssistantAI_api)
- [Carik Bot](https://github.com/luridarmawan/Carik)
- [Github Action for reviewing PRs](https://github.com/kxxt/AssistantAI-action/)
- [WhatsApp Bot #1](https://github.com/askrella/whatsapp-AssistantAI) (DALL-E + Whisper support 💪)
- [WhatsApp Bot #2](https://github.com/amosayomide05/AssistantAI-whatsapp-bot)
- [WhatsApp Bot #3](https://github.com/pascalroget/whatsgpt) (multi-user support)
- [WhatsApp Bot #4](https://github.com/noelzappy/AssistantAI-whatsapp) (schedule periodic messages)
- [WhatsApp Bot #5](https://github.com/hujanais/bs-chat-gpt3-api) (RaspberryPi + ngrok + Twilio)
- [Matrix Bot](https://github.com/matrixgpt/matrix-AssistantAI-bot)
- [Rental Cover Letter Generator](https://sharehouse.app/ai)
- [Assistant CLI](https://github.com/diciaup/assistant-cli)
- [Teams Bot](https://github.com/formulahendry/AssistantAI-teams-bot)
- [Askai](https://github.com/yudax42/askai)
- [TalkGPT](https://github.com/ShadovvBeast/TalkGPT)
- [AssistantAI With Voice](https://github.com/thanhsonng/AssistantAI-voice)
- [iOS Shortcut](https://github.com/leecobaby/shortcuts/blob/master/other/AssistantAI_EN.md)
- [Slack Bot #1](https://github.com/trietphm/AssistantAI-slackbot/)
- [Slack Bot #2](https://github.com/lokwkin/AssistantAI-slackbot-node/) (with queueing mechanism)
- [Slack Bot #3](https://github.com/NessunKim/slack-AssistantAI/)
- [Slack Bot #4](https://github.com/MarkusGalant/AssistantAI-slackbot-serverless/) ( Serverless AWS Lambda )
- [Electron Bot](https://github.com/ShiranAbir/chaty)
- [Kodyfire CLI](https://github.com/nooqta/AssistantAI-kodyfire)
- [Twitch Bot](https://github.com/BennyDeeDev/AssistantAI-twitch-bot)
- [Continuous Conversation](https://github.com/DanielTerletzkiy/chat-gtp-assistant)
- [Figma plugin](https://github.com/frederickk/AssistantAI-figma-plugin)
- [NestJS server](https://github.com/RusDyn/AssistantAI_nestjs_server)
- [NestJS AssistantAI Starter Boilerplate](https://github.com/mitkodkn/nestjs-AssistantAI-starter)
- [Wordsmith: Add-in for Microsoft Word](https://github.com/xtremehpx/Wordsmith)
- [QuizGPT: Create Kahoot quizzes with AssistantAI](https://github.com/Kladdy/quizgpt)
- [stableai-AssistantAI: Talk to AssistantAI from the terminal](https://github.com/gmpetrov/stableai-AssistantAI)
- [Clippy the Saleforce chatbot](https://github.com/sebas00/AssistantAIclippy) ClippyJS joke bot
- [ai-assistant](https://github.com/youking-lib/ai-assistant) Chat assistant
- [Feishu Bot](https://github.com/linjungz/feishu-AssistantAI-bot)

If you create a cool integration, feel free to open a PR and add it to the list.

## Compatibility

- This package is ESM-only.
- This package supports `node >= 14`.
- This module assumes that `fetch` is installed.
  - In `node >= 18`, it's installed by default.
  - In `node < 18`, you need to install a polyfill like `unfetch/polyfill` ([guide](https://github.com/developit/unfetch#usage-as-a-polyfill)) or `isomorphic-fetch` ([guide](https://github.com/matthew-andrews/isomorphic-fetch#readme)).
- If you want to build a website using `AssistantAI`, we recommend using it only from your backend API

## Credits

- Huge thanks to [@waylaidwanderer](https://github.com/waylaidwanderer), [@abacaj](https://github.com/abacaj), [@wong2](https://github.com/wong2), [@simon300000](https://github.com/simon300000), [@RomanHotsiy](https://github.com/RomanHotsiy), [@ElijahPepe](https://github.com/ElijahPepe), and all the other contributors 💪
- The original browser version was inspired by this [Go module](https://github.com/danielgross/whatsapp-gpt) by [Daniel Gross](https://github.com/danielgross)
- [StableAI](https://stableai.com) for creating [AssistantAI](https://stableai.com/blog/AssistantAI/) 🔥

## License

MIT © [Travis Fischer](https://transitivebullsh.it)

If you found this project interesting, please consider [sponsoring me](https://github.com/sponsors/transitive-bullshit) or <a href="https://twitter.com/transitive_bs">following me on twitter <img src="https://storage.googleapis.com/saasify-assets/twitter-logo.svg" alt="twitter" height="24px" align="center"></a>
