# OpenClaw VPS Setup: Hetzner + Tailscale + OpenClaw + Telegram

## 1. Create VPS

1. Go to [hetzner.com/cloud](https://www.hetzner.com/cloud)
2. Create a new project, then **Add Server**
3. Pick cheapest shared CPU plan (CX22)
4. Choose **Ubuntu 24.04**
5. Add your SSH public key (`~/.ssh/id_ed25519.pub`)
6. Create server and copy its public IP

## 2. SSH into server

```bash
ssh root@<SERVER_PUBLIC_IP>
```

## 3. Install Tailscale on VPS

```bash
curl -fsSL https://tailscale.com/install.sh | sh
tailscale up
```

Open URL printed in terminal to authenticate with your Tailscale account.

## 4. Install Tailscale on your Mac

```bash
brew install tailscale
tailscale up
```

Verify both devices see each other:

```bash
tailscale status
```

## 5. Lock down firewall

In Hetzner Cloud Console, go to **Firewalls**, create a new firewall with **one** inbound rule:

| Protocol | Port | Source        |
| -------- | ---- | ------------- |
| TCP      | 22   | 100.64.0.0/10 |

Delete all other inbound rules. Apply to your server.

`100.64.0.0/10` is Tailscale's IP range. Now only your Tailscale devices can SSH in.

## 6. Set up SSH shortcut

Add to `~/.ssh/config` on your Mac:

```
Host <SERVER_NAME>
    HostName <TAILSCALE_IP>
    User root
    IdentityFile ~/.ssh/id_ed25519
```

Now connect with just:

```bash
ssh <SERVER_NAME>
```

## 7. Install OpenClaw on VPS

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

Follow onboarding wizard. When it asks for auth, use your Anthropic API key:

```bash
openclaw onboard
```

## 8. Create Telegram bot

1. Open Telegram, message **@BotFather**
2. Send `/newbot`
3. Pick name and username (must end in `bot`)
4. Copy bot token

## 9. Connect Telegram to OpenClaw

```bash
openclaw channel add telegram
```

Paste bot token when prompted.

## 10. Verify

```bash
openclaw doctor
openclaw status
```

Send message to your bot on Telegram. Approve pairing code shown in terminal.

## Useful commands

```bash
openclaw status          # Check if everything is running
openclaw doctor --fix    # Diagnose and fix issues
openclaw models status   # Check model/auth config
openclaw logs            # View logs
```
