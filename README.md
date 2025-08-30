
# Your Text, Your Style – Presentation Generator

![Presentation generator concept]({{file:file-63m74MxYHacgPVd7NxBrV8}})

A lightweight web application that turns long‑form text or Markdown into a polished PowerPoint presentation.  Upload your own `.pptx` or `.potx` template to preserve your brand’s colours, fonts and layouts, supply an LLM API key (OpenAI, AI Pipe, Anthropic Claude or Google Gemini) and let the backend generate a slide deck that matches your chosen style.  No AI images are generated; instead, the system reuses images from your template to maintain a coherent visual identity【261556983672121†L2-L19】.

## Key Features

| Functionality       | Description |
|--------------------|-------------|
| **Text to Slides**   | Paste large chunks of text or Markdown.  The app uses an LLM to segment the content into a logical sequence of slide titles and bullet points【261556983672121†L8-L16】. |
| **Template Reuse**   | Upload a PowerPoint or template file to preserve layouts, colours and fonts; images from the template are optionally reused on the generated slides【261556983672121†L10-L18】. |
| **LLM Provider Choice** | Provide your own API key for OpenAI, AI Pipe, Anthropic or Gemini.  The key is used only for the current request and never logged【261556983672121†L12-L14】. |
| **Intelligent Slide Count** | The backend chooses a sensible number of slides based on the input length, or you can specify an exact number of slides (1 – 40).  Plans are enforced to ensure a minimum slide count when needed【591322815478138†L123-L142】. |
| **Image Reuse**     | When `reuse_images` is enabled, pictures found in your template are inserted into the corresponding slides while avoiding text overlap.  The algorithm calculates safe zones and moves images when necessary to prevent interference【591322815478138†L497-L546】. |
| **Privacy Friendly** | API keys are accepted via form fields and remain in memory only for the duration of the request【261556983672121†L44-L49】. |

## How It Works

1. **Receive the request**.  The `/generate` endpoint accepts form data containing the input `text`, optional `guidance` (e.g. “investor pitch deck”), provider name, API key and an optional template file【591322815478138†L80-L95】.

2. **Build a slide plan**.  The server calls the chosen LLM to analyse the text and produce a JSON slide plan.  If the user specifies a `num_slides` value, the plan is adjusted to match; otherwise a minimum of ten slides is enforced【591322815478138†L123-L142】.

3. **Prepare the template**.  If a template is supplied, it is loaded using `python‑pptx`.  The existing slides are removed and the title+content layout is identified as a base for new slides.  Images and their positions are extracted for reuse【591322815478138†L497-L524】.

4. **Construct new slides**.  For each slide in the plan, the algorithm determines safe zones that avoid text areas, inserts reused images first, then writes the title and bullet points into either existing placeholders or new text boxes【591322815478138†L497-L595】.  This approach ensures text remains on top of images and nothing overlaps.

5. **Return the `.pptx`**.  Once the deck is built, the bytes are streamed back as a download with the filename `generated.pptx`【591322815478138†L155-L161】.

## Technology Stack

| Layer     | Components |
|----------|-----------|
| **Backend** | FastAPI handles HTTP requests; `python‑pptx` creates and manipulates PowerPoint files; optional SDKs for OpenAI, Anthropic and Google Gemini handle LLM interactions【261556983672121†L20-L24】. |
| **Frontend** | Plain HTML/CSS/JS provides a form for text input, provider selection, API key entry and template upload.  It adapts to light/dark themes and screen sizes. |
| **Optional AI Pipe** | The backend supports AI Pipe via the OpenAI SDK by setting the `base_url` to the AI Pipe endpoint. |

## Running Locally

1. **Clone the repository**:

   ```bash
   git clone https://github.com/Mohdshaadsonofvakeel/PPT_Generator.git
   cd PPT_Generator
   ```

2. **Create a virtual environment and install dependencies**:

   ```bash
   python -m venv .venv && source .venv/bin/activate   # Windows: .venv\Scriptsctivate
   pip install -r requirements.txt
   ```

3. **Run the server**:

   ```bash
   uvicorn app:app --reload
   ```

   The application will be available at `http://localhost:8000`.  Opening this URL shows a simple form where you can paste your text, enter optional guidance, choose your provider and upload a template.

## Deployment

The repository includes a `Procfile` for easy deployment on Render, Railway, Fly.io, Heroku or Hugging Face Spaces【261556983672121†L34-L43】.  A simple Dockerfile is also provided:

```bash
docker build -t presentify .
docker run -p 8000:8000 presentify
```

Deploying to Vercel or Netlify is also possible by serving the `index.html` and proxying API calls to the FastAPI backend.

## API Reference

`POST /generate` – Generate a PowerPoint presentation.

| Parameter     | Type       | Description |
|--------------|-----------|-------------|
| `text`       | `string`   | The input text or Markdown to convert into slides (required). |
| `guidance`   | `string`   | Optional one‑line hint about tone or use case. |
| `provider`   | `string`   | One of `openai`, `aipipe`, `anthropic`, `gemini`. |
| `api_key`    | `string`   | Your API key or token for the chosen provider (required). |
| `model`      | `string`   | Optional model name; defaults are provided for each provider【591322815478138†L38-L46】. |
| `num_slides` | `integer`  | Optional number of slides to generate (1–40). |
| `reuse_images` | `boolean` | Whether to reuse images from the template. |
| `template`   | `file`     | Optional `.pptx` or `.potx` file to extract styles and pictures. |

The response is a streaming download of the generated `.pptx` file.  Errors are returned with appropriate status codes and descriptive messages.

## Privacy & Limitations

* API keys are not stored or logged; they live only in memory for the duration of the request【261556983672121†L44-L49】.
* Extremely long inputs are truncated to fit within provider token limits【261556983672121†L51-L52】.
* Layout inference is heuristic; the system tries to find a `Title + Content` layout but will fall back to the first available layout if none are found.
* Background pictures may not be detected by `python‑pptx`; only visible image shapes on slides are reused【261556983672121†L55-L56】.

## Contributing & Roadmap

Contributions are welcome!  Potential enhancements include:

* Generating speaker notes using the LLM.
* Supporting multiple stylistic modes (e.g. investor pitch, research summary).
* Adding slide previews before download.
* Implementing more robust error handling and file size limits.

See the `README.md` in the repository for basic usage instructions【261556983672121†L0-L19】.

## License

This project is released under the MIT License.
