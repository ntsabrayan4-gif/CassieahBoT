# CassieahBoT 4.0+ (Dev)

bryanBoT (formerly bryanSpectra, forked from bryanRedux, originally bryanBoT) is a multi-platform chatbot framework for Facebook, Discord, and web. Maintained by Liane Cagara ([lianecagara on GitHub](https://github.com/lianecagara)), it uses TypeScript for robust tooling and now includes **napi-rs canvas** for image generation because text-only output is outdated. This is the 4.0+ dev branch, so expect rough edges. If you break something, that’s on you.

<img src="public/Cover.png"></img>

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Changelog](#changelog)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [Configuration](#configuration)
  - [Deployment](#deployment)
- [Writing Commands](#writing-commands)
  - [Command Structure](#command-structure)
  - [Meta Options](#meta-options)
  - [Context Variables](#context-variables)
  - [Example Command](#example-command)
- [Core Files](#core-files)
- [License](#license)

## Overview

bryanBoT is a framework for building bots that work across Facebook (personal accounts and pages), Discord (partially), and web interfaces. It leverages TypeScript for type safety and includes **napi-rs canvas** for generating images dynamically. It’s extensible but not foolproof. If you don’t follow instructions, don’t expect it to work.

## Features

- **TypeScript Tooling**: Strong typing and autocomplete. Use a proper IDE or deal with the consequences.
- **Image Generation**: Uses **napi-rs canvas** to create images like memes or charts. Text output alone won’t cut it anymore.
- **Command Management**: Define and manage commands with a clear structure.
- **Multi-Platform**: Supports Facebook (personal and pages), Discord (limited), and web.
- **Premade Components**: Templates like `UTShop` and `GameSimulator` for common tasks.
- **Auto-Styling**: Commands get formatted titles and lines automatically.
- **MongoDB Abstraction**: Simplified database interaction. Still requires a valid MongoDB URI.
- **Idle Games**: Built-in support for idle game mechanics.
- **Plugins**: Parallel plugin execution with promises. Better than the old system.
- **User Management**: Handles permissions, roles, and inventory systems.
- **Custom Fonts**: Unicode fonts styled like Markdown.
- **API Integration**: Connect to external APIs for additional functionality.

## Changelog

- **4.0+ (Dev)**:
  - Added **napi-rs canvas** for image generation.
  - Improved plugin system with proper promise handling.
  - Simplified MongoDB abstraction.
  - Performance optimizations.
  - Discord support remains partial and undertested.
  - Fixed some bugs. New ones are probably lurking.

## Getting Started

Follow these steps exactly. If the bot doesn’t work, check the logs first before asking for help.

### Prerequisites

- GitHub account.
- Facebook Page (required for Messenger integration).
- Dummy Facebook account (don’t use your main one).
- Browser with extension support (e.g., Chrome, Edge, Kiwi).
- **Cookie Editor** or **C3C FBState** extension for exporting cookies.
- Railway account ([railway.app](https://railway.app)).
- Node.js 23.7.0 or higher. Older versions will fail.
- MongoDB URI (e.g., from MongoDB Atlas).
- Basic understanding of JavaScript/TypeScript and command-line tools.

### Installation

1. **Fork or Generate the Repository**:
   - Go to [https://github.com/lianecagara/CassieahBoT](https://github.com/lianecagara/CassieahBoT).
   - Fork or use the template to create your own repo (`https://github.com/your-username/CassieahBoT`).
   - Template is better for private repos.

2. **Clone the Repository**:
   ```bash
   git clone https://github.com/your-username/bryanBoT
   cd bryanBoT
   ```

3. **Run Update Command**:
   - Before touching *any* files, run:
     ```bash
     npm run update
     ```
   - This ensures dependencies and configs are up to date. If you skip this and modify files, updates will break. Don’t say I didn’t warn you.

4. **Install Dependencies**:
   ```bash
   npm install
   ```
   - If this fails, verify Node.js version (23.7.0+).

5. **Install napi-rs Canvas**:
   ```bash
   npm install @napi-rs/canvas
   ```
   - Linux users may need additional packages (`libcairo2-dev`, `libpango1.0-dev`). Search for solutions if it fails.

6. **Set Up Cookies**:
   - Log in to your dummy Facebook account.
   - Use **Cookie Editor** or **C3C FBState** to export cookies in JSON format.
   - Replace the contents of `cookie.json` with your cookies.
   - Run `node hidestate` if using an `.env` file to secure appstate.
   - **Warning**: Never commit `cookie.json` to a public repo. Use environment variables.

### Configuration

1. **Facebook Messenger Setup**:
   - Go to [developers.facebook.com](https://developers.facebook.com).
   - Create a Business app.
   - Add the **Messenger** product.
   - Link your Facebook Page and generate a **Page Access Token**.
   - Configure webhooks:
     - **Callback URL**: `https://your-railway-url/webhook` (set after deployment).
     - **Verify Token**: Create one (e.g., `pagebot`) and note it.
     - Subscribe to `messages`, `messaging_optins`, `messaging_postbacks`.
   - Update `settings.json`:
     ```json
     {
       "pageAccessToken": "your-page-access-token",
       "pageVerifyToken": "your-verify-token",
       "discordBotToken": "",
       "discordClientID": ""
     }
     ```
   - **Warning**: Keep tokens secure. Use environment variables.

2. **Discord (Optional)**:
   - Go to [Discord Developer Portal](https://discord.com/developers/applications).
   - Create a bot and copy its token.
   - Add to `settings.json`.
   - Discord support is partial. Test thoroughly or stick to Facebook.

3. **Database**:
   - Add your MongoDB URI to `.env`:
     ```env
     MONGO_URI="your-mongodb-uri"
     ```
   - Get a URI from MongoDB Atlas if you don’t have one.

### Deployment

1. **Local Testing**:
   ```bash
   npm start
   ```
   - If it fails, check `cookie.json`, `settings.json`, or logs.

2. **Railway Deployment**:
   - Log in to [railway.app](https://railway.app).
   - Create a new project and select your CassieahBoT repo.
   - Ensure the Dockerfile uses Node.js 23.7.0+.
   - Set environment variables:
     - `MONGO_URI`: Your MongoDB URI.
     - `APPSTATE`: Contents of `cookie.json` (or use the file).
   - Deploy to **USA Oregon** region. Other regions won’t work.
   - Get the Railway URL (e.g., `https://your-project-name.up.railway.app`).
   - Update Facebook webhook with `/webhook` (e.g., `https://your-project-name.up.railway.app/webhook`).

3. **Testing**:
   - Message your Facebook Page with “help” from a non-dummy account.
   - No response? Check Railway logs. It’s usually a misconfigured token or role.
   - Assign roles in [developers.facebook.com](https://developers.facebook.com) under **App Roles** to allow specific accounts to interact.

## Writing Commands

### Command Structure

Commands are defined in JavaScript/TypeScript files. Here’s the format:

```typescript
export const meta: CommandMeta = {
    name: "example",
    otherNames: ["ex", "test"],
    author: "Your Name",
    version: "1.0.0",
    description: "Basic command example.",
    usage: "{prefix}{name} [arg]",
    category: "Misc",
    role: 0,
    waitingTime: 5,
};

export async function entry({ input, output, args }: CommandContext) {
    output.reply(`You said: ${args[0] || "nothing"}`);
}
```

## License

See the `LICENSE` file for details.

## Credits

- **MrKimstersDev (Symer Steve Berondo)**: Original tutorial.
- **Liane Kaye Cagara**: Creator and maintainer.

---

**Important**: Always run `npm run update` before editing any files, or updates will break. If the bot isn’t responding, check the logs (Railway or local). Most issues are due to misconfigured tokens, cookies, or roles. Don’t skip steps, and don’t expect it to work if you half-read this.

## Ultimate Guide to Writing Commands in the CassieahBoT Messenger Bot Framework  
*(Current as of December 24, 2025 – Modern Best Practices Only)*

This is the **final, production-hardened reference**.  
Every pattern below is used daily in the largest CassieahBoT economies.  
Prioritized from **most critical** to advanced.

---

### 1. `ctx` – The Core Context Object (Most Important)

`ctx` is passed to **every** command handler. It contains everything.

**Always destructure directly in parameters** (mandatory style):

```ts
async handler({ input, output, money }) {
  // Now use input, output, money directly
}
```

This is the cleanest, fastest, and most consistent way.

Do **not** do this:
```ts
async handler(ctx) {
  const { input, output, money } = ctx; // Verbose, unnecessary
}
```

Do **not** access via `ctx.input.body` — always destructure in params.

---

### 2. Command Metadata (`meta: CommandMeta`) – Required Fields

```ts
export const meta: CommandMeta = {
  name: "vault",
  otherNames: ["v", "safe"],
  description: "Secure storage with deposit/withdrawal fees",
  usage: "{prefix}{name} [deposit|withdraw|list]",
  category: "Economy",
  version: "4.0.0",
  icon: "🔒",
  waitingTime: 3,   // cooldown in seconds per user
  role: 0,          // 0=everyone, 1=thread admin+, 1.5=moderator+, 2=bot admin
};
```

**`waitingTime`** → automatic per-user cooldown  
**`role`** → automatic block if insufficient

Read current user role:
```ts
input.role  // returns 0, 1, 1.5, or 2
```

---

### 3. Command Style – Simple & Powerful

```ts
export const style: CommandStyle = {
  title: "🔒 Vault System",   // string = auto-styled title
  contentFont: "fancy",
  lineDeco: "altar",          // or "x"
};
```

**If `style` is exported**, `output.reply(text)` and `output.send(text)` **automatically apply it**.

```ts
await output.reply("Styled automatically")
await output.send("Also styled", "thread123")
```

Only use `replyStyled` when overriding.

---

### 4. Command Backbone – Two Options

#### A. Simple (≤4 subcommands) → `defineEntry`

```ts
export const entry = defineEntry({
  async deposit({ input, output, money }) {
    // logic
  },
  async withdraw({ input, output, money }) {
    // logic
  },
  async list({ input, output, money }) {
    // logic
  },
});
```

#### B. Complex (5+ subcommands) → `SpectralCMDHome`

```ts
const home = new SpectralCMDHome({}, [
  {
    key: "deposit",
    aliases: ["dep"],
    async handler({ input, output, money }) {
      // logic
    }
  },
  {
    key: "withdraw",
    aliases: ["wd"],
    async handler({ input, output, money }) {
      // logic
    }
  },
]);

return home.runInContext(ctx);
```

---

### 5. `input` – Message Intelligence

```ts
input.body                     // full message
input.words                    // ["vault", "deposit", "1000"]
input.args                     // ["deposit", "1000"]
input.senderID                 // user ID
input.messageReply?.senderID   // replied user
input.mentions                 // { "@Name": "uid" }
Object.values(input.mentions)[0] // first @ UID
input.hasMentions              // true/false
input.role                     // 0–2
input.isGroup                  // group chat?
```

```ts
input.test(/all/i)             // regex
input.isMessage()              // text message
input.hasWordOR("all", "max")  // any word
input.hasWordAND("send", "money") // all words
input.equal("yes")             // exact
input.lower().body             // lowercase
```

```ts
// Detect target user (used in transfers, trades, duels)
const target = input.messageReply?.senderID || Object.values(input.mentions)[0];
```

---

### 6. `output` – Messaging Power

```ts
await output.reply("Auto-styled")
await output.send("To thread", "thread123")
await output.attach("Caption", "https://img.jpg")
await output.edit("Update", "mid123")
await output.unsend("mid123")
await output.reaction("❤️", "mid123")
await output.wentWrong()
await output.error(new Error("Fail"))
```

```ts
const msg = await output.reply("Question?");
msg.editSelf("Updated")
msg.unsendSelf()
msg.atReply(async ({ input, output }) => {
  // handle reply
})
```

```ts
await output.confirm("Proceed?", async ({ yes }) => {
  if (yes) await output.reply("Done");
})
```

---

### 7. Database – `money`

```ts
const user = await money.getCache(input.senderID)
await money.setItem(input.senderID, {
  money: user.money + 5000,
  lastUsed: Date.now(),
})
```

```ts
// Top 10 richest (used for leaderboards)
const top = (await money.getAllCache())
  .sort((a, b) => (b.money ?? 0) - (a.money ?? 0))
  .slice(0, 10);
```

---

### 8. Inventory – `new Inventory()`

```ts
const inv = new Inventory(user.items ?? []);

inv.has("sword")
inv.getAmount("potion")
inv.addOne({ key: "gem_123", name: "Ruby", icon: "💎" })
inv.toss("potion", 5)
inv.deleteOne("temp_456")

await money.setItem(input.senderID, {
  items: Array.from(inv),
})
```

```ts
// Display inventory (used in !inv, !vault list)
inv.toUnique()
  .map(i => `${i.icon} **${i.name}** ×${inv.getAmount(i.key)}`)
  .join("\n")
```

---

### 9. Bad Practices – Never Do These

| Bad Practice                                   | Why It's Bad                                      |
|------------------------------------------------|---------------------------------------------------|
| `async handler(ctx) { const { ... } = ctx; }`  | Verbose — destructure in parameters               |
| `ctx.input.body` instead of destructured       | Pollutes code, harder to read                     |
| Using arrow functions for handlers             | Breaks context in rare cases                       |
| Using `function()` syntax                      | Outdated, verbose                                 |
| Manual role checks (`if (input.role < ...)`)   | Framework handles it automatically                |
| Using `replyStyled` when `style` is exported   | Redundant — `reply` auto-applies style            |
| Not using `Array.from(inventory)` on save      | Data loss — required for persistence              |
| Using deprecated fields (`permissions`, etc.)  | Ignored by framework                              |
| Not exporting `style`                          | Misses auto-styling benefit                       |

---

### 10. Real-World Complete Example – Vault Deposit (Production Pattern)

This is how **real vault/bank deposit** commands are written in large economies.

```ts
{
  key: "deposit",
  async handler({ input, output, money }) {
    const amount = parseBet(input.words[1], Infinity);
    if (!amount) return output.reply("Invalid amount — use number or 'all'");

    const user = await money.getCache(input.senderID);
    if ((user.money ?? 0) < amount) return output.reply("Not enough money in wallet");

    const fee = Math.floor(amount * 0.03);     // 3% deposit fee
    const net = amount - fee;

    const msg = await output.reply(
      `Deposit **${formatCash(amount)}**?\n` +
      `Fee: **${formatCash(fee)}** (3%)\n` +
      `Net stored: **${formatCash(net)}**\n\n` +
      `Reply **yes** to confirm`
    );

    msg.atReply(async ({ input: confirmInput, output: confirmOutput }) => {
      if (confirmInput.body.toLowerCase() !== "yes") {
        return confirmOutput.reply("Deposit cancelled");
      }

      const updated = await money.getCache(input.senderID);
      if ((updated.money ?? 0) < amount) {
        return confirmOutput.reply("Balance changed — insufficient funds");
      }

      const vault = new Inventory(updated.vaultItems ?? []);
      vault.addOne({
        key: `dep_${Date.now()}`,
        name: "Vault Deposit",
        icon: "💰",
        value: net,
      });

      await money.setItem(input.senderID, {
        money: updated.money - amount,
        vaultItems: Array.from(vault),
      });

      await confirmOutput.reply(`Deposited **${formatCash(net)}** after fee!`);
    });
  }
}
```

This pattern is used for:
- Bank deposits
- Item storage
- Tax payments
- Trade confirmations
- Any high-value transaction

---

**bryanBoT — December 24, 2026**  
Destructure in parameters. Export `style`. Use `output.reply()`.  
Clean. Fast. Scalable.

Now go build the next trillion-dollar virtual economy.  
💰🔒
