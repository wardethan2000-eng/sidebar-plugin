# Sidebar plugin marketplace

This repository is a Claude plugin marketplace with one plugin, **Sidebar**: the Sidebar case-law
connector plus skills for litigation work (potential claims, motion to dismiss, summary judgment,
and checking the other side's brief). See [plugins/sidebar/README.md](plugins/sidebar/README.md).

## Install in Claude

1. In Claude (web or desktop), open **Plugins** in the sidebar (called **Customize** on some accounts).
2. Click **Add → Add marketplace → Add from a repository** and enter `wardethan2000-eng/sidebar-plugin`.
3. Open **Discover**, choose **Sidebar**, and click **Add**.
4. On the plugin's **Connectors** tab, connect Sidebar and sign in (or create an account).

In Claude Code: `/plugin install sidebar --marketplace wardethan2000-eng/sidebar-plugin`

## Releasing a change

Edit the plugin under `plugins/sidebar/`, bump `version` in `plugins/sidebar/.claude-plugin/plugin.json`,
and push to `main`.
