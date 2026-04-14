# WiFi Setup for the Obi-Wan firmware

The Obi-Wan voice mode lives in `examples/factory_demo` and posts audio to the `obi-wan-core` bridge on your LAN. To do that, the BOX needs your WiFi SSID and password.

**Never commit credentials to this repo.** Keep your modifications local.

## One-time setup

Edit `examples/factory_demo/main/app/app_wifi.c`, find the `#if CONFIG_BSP_BOARD_ESP32_S3_BOX_3` block around line 225, and fill in your network:

```c
wifi_config_t wifi_cfg = {
    .sta = {
        .ssid     = "YourSSID",
        .password = "YourPassword",
        .listen_interval = DEFAULT_LISTEN_INTERVAL,
    },
};
```

You also need to update the bridge URL in `examples/factory_demo/main/app/app_sr.c`:

```c
#define BRIDGE_URL "http://<your-obi-wan-core-host>:9090/talk"
```

## Keep your secrets out of git

Once you've set the credentials, run this (from the repo root) so the file's local modifications never appear in `git status` or get committed by accident:

```bash
git update-index --skip-worktree examples/factory_demo/main/app/app_wifi.c
```

To undo later (e.g. to pull upstream changes to this file):

```bash
git update-index --no-skip-worktree examples/factory_demo/main/app/app_wifi.c
```

If you ever accidentally commit credentials, rewrite history with `git filter-repo` or BFG **before** pushing. Force-pushing credentials that already landed on a public remote is not a fix — rotate the password.
