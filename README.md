# This project is a fork of [tauri-plugin-localhost](https://github.com/tauri-apps/tauri-plugin-localhost)

![plugin-localhost](banner.png)

[DEPRECATED] Please use the plugin from https://github.com/tauri-apps/plugins-workspace instead.

Expose your apps assets through a localhost server instead of the default custom protocol.

> Note: This plugins brings considerable security risks and you should only use it if you know what your are doing. If in doubt, use the default custom protocol implementation.

## Install

_This plugin requires a Rust version of at least **1.64**_

There are three general methods of installation that we can recommend.

1. Use crates.io and npm (easiest, and requires you to trust that our publishing pipeline worked)
2. Pull sources directly from Github using git tags / revision hashes (most secure)
3. Git submodule install this repo in your tauri project and then use file protocol to ingest the source (most secure, but inconvenient to use)

Install the Core plugin by adding the following to your `Cargo.toml` file:

`src-tauri/Cargo.toml`

```toml
[dependencies]
omuchat-tauri-plugin-server = { git = "https://github.com/OMUCHAT/tauri-plugin-server", branch = "v1" }
portpicker = "0.1" # used in the example to pick a random free port
```

## Usage

First you need to register the core plugin with Tauri:

`src-tauri/src/main.rs`

```rust
use tauri::{utils::config::AppUrl, window::WindowBuilder, WindowUrl};

fn main() {
  let port = portpicker::pick_unused_port().expect("failed to find unused port");

  let mut context = tauri::generate_context!();
  let url = format!("http://localhost:{}", port).parse().unwrap();
  let window_url = WindowUrl::External(url);
  // rewrite the config so the IPC is enabled on this URL
  context.config_mut().build.dist_dir = AppUrl::Url(window_url.clone());
  context.config_mut().build.dev_path = AppUrl::Url(window_url.clone());

  tauri::Builder::default()
    .plugin(omuchat_tauri_plugin_server::Builder::new(port).build())
    .setup(move |app| {
      WindowBuilder::new(app, "main".to_string(), window_url)
        .title("Localhost Example")
        .build()?;
      Ok(())
    })
    .run(context)
    .expect("error while running tauri application");
}
```

## Contributing

PRs accepted. Please make sure to read the Contributing Guide before making a pull request.

## License

Code: (c) 2015 - Present - The Tauri Programme within The Commons Conservancy.

MIT or MIT/Apache 2.0 where applicable.
