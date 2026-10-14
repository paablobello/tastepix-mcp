---
name: tastepix
description: Make and edit images with Tastepix. Use when the user asks to create, draw, generate or design an image (photo, illustration, poster, logo, icon, OG image, hero image), to edit or combine images, remove a background, upscale, or turn an image into a short video.
---

# Tastepix

Tastepix is a hosted MCP server (`https://mcp.tastepix.com/mcp`) that generates and edits images
with many models, billed per image from the user's prepaid Tastepix balance. Its tools come from
the `tastepix` MCP server.

## If Tastepix is not connected

If the Tastepix tools (`generate_image` and the rest) are not available, the server is not connected
or not signed in yet. Point the user to the install steps at
https://github.com/paablobello/tastepix-mcp#connect and stop. In Claude Code, `/mcp` → **tastepix**
→ sign in. Never ask for a password or an API key: sign-in happens in the browser.

## Generate and edit

- **`generate_image`** makes an image from a `prompt`, or edits and combines images passed as
  references. Call it **once per request**. The result is already shown to the user inline: do not
  call it again to refine, retry or verify, and do not follow it with `show_images`. Generate again
  only when the user asks for changes or more images.
- **Model.** Map the model the user names straight to an id from the `model` parameter's list (for
  a family with several variants, such as "GPT Image" or "Nano Banana", use the first one listed).
  Leave it at the default when they name none. Never ask the user to choose a model or variant.
- **Edits.** Pass the image to edit in `reference_asset_ids` (an `asset_id` from an earlier result or
  upload) or `reference_urls` (public https URLs), and describe the change in `prompt`.
- **Shape and size.** `aspect_ratio` such as `1:1`, `3:4`, `16:9` (unsupported ratios map to the
  nearest one; omit it when editing to keep the reference's ratio). `resolution` such as `1K`, `2K`,
  `4K` where the model offers it.
- **After generating**, reply briefly. Call the model by its name (e.g. "GPT Image 2.5"), not its id.
  Don't repeat the image URLs unless the user asks. Vector results include `svg_url`: give the user
  that link for the SVG file.
- **Saving into a project** (coding agents): download the result's `url` (or `svg_url` for vector
  results) with curl to the path the user asked for.

## Work on an existing image

Pass its `asset_id`. Use these when the user asks for them, instead of regenerating:

- **`remove_background`**: cuts out the subject into a transparent PNG.
- **`upscale_image`**: faithful `factor` 2 or 4 upscale (default 2).
- **`animate_image`**: short video with sound; optional `prompt` for the motion, `seconds` 5, 8 or
  10 (default 5). It returns at once and the viewer shows the video when it is ready. Don't call it
  again for the same request; if the user asks about it later, call **`get_video`** with the
  `generation_id`.
- **`show_images`**: shows earlier images again by `asset_ids`. Not needed after `generate_image`.

## Local images

Files the user attached or has on disk can't be passed directly; they must be uploaded first.

- **Chat apps:** call **`upload_images`** to show an inline drop zone, then wait. The user's next
  message contains the new `asset_ids`; use them without asking for confirmation.
- **Agents that can run code** (Claude Code, Codex, Cursor, Gemini CLI): call **`request_upload`**
  with the file's `mime_type` (`image/png`, `image/jpeg` or `image/webp`). It returns an `asset_id`,
  a one-time `upload_url` (valid 15 minutes) and a ready `curl_command`:

  ```bash
  curl -sS -X PUT -H "Content-Type: image/png" --data-binary @"<path-to-file>" "<upload_url>"
  ```

  Run it with the real path, then pass the `asset_id` in `reference_asset_ids` (or to
  `remove_background`, `upscale_image`, `animate_image`). One file per `request_upload` call.

## Models and money

- Call **`list_models`** only when the user asks for advice, prices or a model that is not listed.
- Don't mention cost or generation time unless the user asks.
- If a call fails for **insufficient balance** or the daily limit, tell the user and pass on the
  link from the error message if it has one. Do not retry.
- If a call says too many images are in progress or asks you to wait, wait; don't retry right away.

<!-- BEGIN upcoming: count and vectorize_image. Ship only once both are live on the server. -->
## Newer tools (use only if the server lists them)

- **Several images at once:** if `generate_image` has a `count` parameter, use it (e.g. `count: 4`)
  when the user wants several options, instead of calling `generate_image` several times.
- **`vectorize_image`**: if the server has this tool, use it to turn an existing raster image
  (`asset_id`) into an SVG, for logos and icons the user wants as vectors.
<!-- END upcoming -->
