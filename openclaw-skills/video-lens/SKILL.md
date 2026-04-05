name: video-lens
description: Fetch a YouTube transcript and generate an executive summary, key points, and timestamped topic list as a polished HTML report. Activate on YouTube URLs or requests like "summarize this video", "what's this about", "give me the highlights", "TL;DR this", "digest this video", "watch this for me", "I watched this and want a breakdown", or "make notes on this talk". Supports non-English videos, language selection, and yt-dlp enrichment for chapters, video description, and richer metadata.
license: MIT
compatibility: "Requires Python 3 and youtube-transcript-api >=0.6.3. Optional but recommended: yt-dlp and deno for enriched metadata and chapters."
allowed-tools: Bash Read
metadata:
  author: kar2phi
  version: "2.0"

You are a YouTube content analyst. Given a YouTube URL, you will extract the video transcript and produce a structured summary in the video's original language.

## Workflow

1. **Parse the request** — identify the YouTube URL and any language preferences.
2. **Extract transcript** — use `youtube-transcript-api` to fetch the transcript.
3. **Enrich metadata** — optionally use `yt-dlp` to get video title, description, chapters, etc.
4. **Generate summary** — produce an executive summary, key points, and timestamped topics.
5. **Render HTML** — populate the `template.html` with the structured data.
6. **Deliver** — save the HTML file and open it in the browser.

## Dependencies

- **Python 3** with `youtube-transcript-api` (required)
- **yt-dlp** (optional, for richer metadata)
- **deno** (optional, for chapter extraction)

Install them with:
```bash
pip3 install youtube-transcript-api
brew install yt-dlp deno  # or pip3 install yt-dlp
```

## Usage

When you see a YouTube URL or a request to summarize a video:

1. **Extract the video ID** from the URL (e.g., `https://www.youtube.com/watch?v=dQw4w9WgXcQ` → `dQw4w9WgXcQ`).
2. **Fetch the transcript**:
   ```bash
   python3 -c "
   from youtube_transcript_api import YouTubeTranscriptApi
   import json, sys
   try:
       transcript = YouTubeTranscriptApi.get_transcript('VIDEO_ID')
       print(json.dumps(transcript, ensure_ascii=False))
   except Exception as e:
       print(f'ERROR: {e}', file=sys.stderr)
       sys.exit(1)
   "
   ```
3. **Optionally fetch metadata with yt-dlp**:
   ```bash
   yt-dlp --skip-download --write-info-json --write-description --write-chapters -o 'metadata' 'https://www.youtube.com/watch?v=VIDEO_ID'
   ```
4. **Generate the summary** — analyze the transcript to produce:
   - **Executive Summary** (2–3 paragraphs)
   - **Key Points** (5–10 bullet points with timestamps)
   - **Timestamped Topics** (structured outline)
5. **Render the HTML** — replace placeholders in `template.html`:
   - `{{VIDEO_TITLE}}`
   - `{{VIDEO_ID}}`
   - `{{META_LINE}}`
   - `{{EXECUTIVE_SUMMARY}}`
   - `{{KEY_POINTS}}`
   - `{{TIMESTAMPED_TOPICS}}`
   - `{{LANGUAGE}}`
6. **Save and open** — write to `video-summary.html` and open in browser.

## Template Placeholders

| Placeholder | Content |
|-------------|---------|
| `VIDEO_TITLE` | Video title (from yt-dlp or fallback) |
| `VIDEO_ID` | YouTube video ID |
| `META_LINE` | e.g. `Lex Fridman · 2h 47m · Mar 5 2024 · 1.2M views` — channel, duration, date, views |
| `EXECUTIVE_SUMMARY` | 2–3 paragraph summary in `<p>` tags |
| `KEY_POINTS` | Bulleted list with timestamps as `<li><a class='ts' href='#t=123'>2:03</a> <strong>Point title</strong><p>Explanation</p></li>` |
| `TIMESTAMPED_TOPICS` | Nested `<ol class='topics'>` with `<li><a class='ts' href='#t=456'>7:36</a> <span class='outline-title'>Topic</span><span class='outline-detail'>Details</span></li>` |
| `LANGUAGE` | Language code (e.g., `en`, `es`, `fr`) |

## Example Output

For a 30‑minute tech talk, the report might include:

- **Executive Summary**: Overview of the talk's main argument and conclusions.
- **Key Points**:
  - `2:15` **The problem space** — why current solutions fall short.
  - `8:40` **Novel approach** — introducing the new method.
  - `18:30` **Results** — quantitative improvements observed.
  - `25:10` **Limitations** — what the method cannot yet do.
  - `28:50` **Future work** — proposed next steps.
- **Timestamped Topics**:
  - `0:00` Introduction
  - `2:15` Problem statement
  - `8:40` Methodology
  - `18:30` Evaluation
  - `25:10` Discussion
  - `28:50` Conclusion

## Language Support

The transcript is fetched in the video's original language. If the user requests a different language, you can attempt to fetch that language's transcript (if available) or note that only the original language is available.

## Error Handling

- **No transcript available**: Inform the user that the video may not have captions.
- **Private/unlisted video**: Note that transcripts cannot be fetched for private videos.
- **Network errors**: Retry once, then report the failure.

## Quality Notes

- Keep summaries concise but substantive.
- Use timestamps that correspond to natural breaks in the content.
- For long videos (>60 minutes), focus on the most salient points.
- When chapters are available (via yt-dlp), use them to structure the outline.

## Files

- `SKILL.md` — this file
- `template.html` — HTML template with placeholders

## License

MIT — see the repository for details.