# Tastepix

The `tastepix` MCP server generates and edits images with many models (Nano Banana, GPT Image,
FLUX, Seedream, Recraft and more), removes backgrounds, upscales, and turns images into short
videos. Each image is billed to the user's prepaid Tastepix balance.

- The first call opens a Tastepix sign-in in the browser. Never ask for a password or an API key.
- Call `generate_image` once per request; don't call it again to refine, retry or verify unless the
  user asks. Never ask the user to pick a model: use the one they name, or the default.
- To edit an image, pass its `asset_id` in `reference_asset_ids`. Local files: call `request_upload`
  with the file's `mime_type`, run the returned `curl_command` with the real path, then use the
  returned `asset_id`.
- To save a result into the project, download its `url` with curl.
- If a call fails for insufficient balance, tell the user and pass on the link from the error.
  Do not retry.
- Full guide: `skills/tastepix/SKILL.md` in this extension.
