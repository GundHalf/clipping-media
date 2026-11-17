# YouTube uploader contract (for the existing OAuth pipeline)

Claude (cloud) cannot reach Google APIs and must never hold client_secret / refresh tokens.
So: Claude fills `youtube_queue.json` + pushes the video files here; the existing pipeline
(OAuth client of project 684581150279 or 516492484238) does the upload.

## One-time setup (Erwin / pipeline owner)
1. Authorize BOTH channels on the existing OAuth client (access_type=offline, prompt=consent),
   choosing the right channel: Killfeed Wire (killfeedwire@gmail.com), Went Live Clips (wentliveclips@gmail.com).
   Store tokens only in the pipeline's secrets file - never in chat or git.
2. Consent screen: Publish app (needs a public Privacy Policy URL) so tokens don't expire after 7 days.
3. Phone-verify both channels if custom thumbnails are needed (not needed now).

## Test before any campaign post
Upload one clip as unlisted (or private + publishAt in ~15 min) to each channel, then
videos.list part=status -> must become public/unlisted, NOT locked private.

## Per item in youtube_queue.json with status "queued"
- download video_url (public GitHub Pages, video/mp4)
- videos.insert: snippet{title, description, categoryId}, status{privacyStatus:"private",
  publishAt, selfDeclaredMadeForKids:false}; paidProductPlacementDetails.hasPaidProductPlacement = contains_paid_promotion
- write back status "scheduled" + youtube_video_id (commit to this repo), or "error" + message
- after publish_at: confirm privacyStatus=public

Privacy Policy URL for the consent screen: https://gundhalf.github.io/clipping-media/privacy.html

Claude then submits https://youtube.com/shorts/<youtube_video_id> on Content Rewards within 30 min of publish_at.
